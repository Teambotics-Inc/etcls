# Vercel Static Configuration Remediation

Status: **Resolved on master.** This document records the rejected configuration, the verified resolution, and release checks for any future Vercel configuration change.
Date: 2026-09-13
Repository: [nickhilster/etcls](https://github.com/nickhilster/etcls)
Affected Vercel project: etlcs (master)

## Purpose

Prevent a recurrence of the Vercel configuration-schema failure while preserving the site's static HTML deployment. This repository is a small multi-page site whose current HTML files live at the repository root.

## Incident and root cause

Commit [d10c78d](https://github.com/nickhilster/etcls/commit/d10c78d21e839930a5d243c086955aebd6de8ce2) added a vercel.json containing the unsupported property "public": true. Vercel rejected the file before the build step with:

> The vercel.json schema validation failed with the following message: should NOT have additional property public

The same file set outputDirectory to public, although the current site is served from root-level HTML files. The deployment failed on branch master at commit d10c78d in the Git-linked Vercel project etlcs.

## Scope and guardrails

This spec covers static hosting configuration and deployment verification only. It does not authorize edits to the site's HTML, project settings, domains, or application content. Keep the repo's static-page behavior intact. Do not copy legacy configuration keys into a new vercel.json.

The Vercel project name is etlcs; its linked GitHub repository is nickhilster/etcls. A separate Vercel project named etcls exists and has a different deployment source. A successful deployment there does not verify the Git-linked etlcs project.

## Remediation steps

The rejected configuration has already been removed. The current repository tree has root-level HTML files and no vercel.json. For any future hosting change:

1. Keep root-level HTML files served by Vercel's default static handling unless a concrete build-output requirement needs custom configuration.
2. If custom configuration becomes necessary, check the current Vercel project-configuration schema and use only supported properties. Do not add the rejected public property. Set an output directory only when the actual build produces it.
3. Validate the change in a preview deployment from the Git-linked etlcs project and confirm the reported repository, branch, and commit.
4. Verify that Vercel gets past configuration validation, serves the expected HTML routes, and reports the deployment READY before promoting a production change.

## Acceptance checks

A future configuration change is ready to roll out only when:

- Vercel accepts the configuration without an “additional property public” error.
- The deployment is from nickhilster/etcls, branch master or its intended preview branch, in project etlcs.
- The deployment reaches READY, and the build/deployment log contains no configuration-schema failure.
- The production root page and representative pages such as /about.html and /services.html return the expected content after rollout.

Do not treat a successful deployment in the separate etcls Vercel project as acceptance evidence for etlcs.

## Rollout and verification

Use the linked etlcs project for preview and production verification. Check the deployment's project, source, branch, commit SHA, and READY state. After promotion, verify the root page and each route affected by the change. If schema validation fails, stop rollout and remove or replace the unsupported configuration before retrying.

## Resolution evidence

The first successful production deployment after the rejected d10c78d commit was 2e3273d (“Move files to root and use static vercel config”). Commit [eaa17e1](https://github.com/nickhilster/etcls/commit/eaa17e128717b5520f9759174de5d15e7d7490c5) subsequently removed vercel.json to use default static serving. The current master tree at 3213140 contains the root-level HTML pages and no Vercel config file. The latest observed etlcs production deployment at that commit is READY ([deployment](https://vercel.com/teambotics/etlcs/9idqjDENQ2WPC2AN3WZyZzVpUbkm)).

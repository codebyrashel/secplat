# 2. Use a pnpm workspace monorepo

Status: Accepted

Date: 31/10/26

## Context
The project has at least two applications (a NestJS API and a Next.js
web app) and will gain shared code such as types and validation rules,
and possibly a command-line scanning agent. We need a way to organise
them and a package manager to install their dependencies. Because the
product is a security tool, how dependencies are installed also matters
for our own supply chain safety.

## Decision
We keep all applications in one Git repository, managed as a pnpm
workspace:

- apps/ holds deployable applications (api, web)
- packages/ holds shared libraries
- pnpm is the only package manager. npm and yarn are not used.

## Alternatives considered
- Separate repositories per app: simpler tooling for each repo, but
  shared code needs publishing, and a change touching the API and the
  web app needs several pull requests.
- npm or yarn workspaces: also support monorepos, but pnpm installs
  faster, uses one shared store on disk, and refuses imports of
  packages a project did not declare.
- A build tool such as Nx or Turborepo on top: useful later for build
  caching, unnecessary now. Revisit if build times grow.

## Consequences
- One clone, one history, one place for documentation and CI.
- Shared packages can be used by both apps without publishing.
- pnpm 11 and later hold back newly published versions for a day by
  default and block dependency install scripts unless allowed. This
  reduces supply chain risk. When a legitimate package needs a build
  script, we approve it explicitly.
- Cost: more setup upfront, and every contributor must use pnpm.

# pnpm Peer Dependency Deadlock Reproduction Repository

## Background

`pnpm install` may hang when resolving peers under certain alias-related layouts (discussion and fix in progress; see links below).

- [Issue pnpm/pnpm#9673 – Installation hangs indefinitely when resolving peers](https://github.com/pnpm/pnpm/issues/9673)
- [PR pnpm/pnpm#9675 – Potential fix](https://github.com/pnpm/pnpm/pull/9675)

This repo offers a minimal reproduction.

## Reproduction steps

The commands below assume you have cloned this repo and are in its root directory.

```bash
# Install the mock registry globally
pnpm install -g @pnpm/registry-mock

# Clean & initialize virtual registry store
registry-mock prepare

# Start the local mock registry (default port 4873)
registry-mock
```

Open another terminal and run:

```bash
# Publish the mock packages in `mock-packages` to the local mock registry
cd mock-packages
for dir in */; do (cd "$dir" && npm publish --registry=http://localhost:4873); done

# Go back to the repo root and install
cd ..
pnpm install --no-lockfile --registry=http://localhost:4873
```

If you run the same steps with pnpm version 9.13.2 - 10.12.1, the installation should hang infinitely.

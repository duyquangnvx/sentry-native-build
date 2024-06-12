# Prebuilt sentry-native

This project builds the official [sentry-native](https://docs.sentry.io/platforms/native/) binaries for and 22.04 using GitHub actions runners.

## How to use this repo

So you need to build a specific version of sentry-native. Here's how:

1. create a branch for the version you want (use whatever name seems sensible)
2. update `.github/workflows/main.yml` to specify the version of sentry-native you want to build
3. create a PR with your changes. This will trigger the CI to do a test build
4. debug any issues
    1. check the action suceeded
    2. check the artefacts it built are correct/work for you
    3. keep tweaking `main.yml` until the action builds the version you want successfully
5. once you have a successful build, merge/rebase your PR into `main`
6. Then tag the latest commit on `main` with the sentry-native version number to trigger a release build (`git tag v6.7.1 && git push --tags`)

Now to use the library you have built do something like:

Use `curl` to download and extract to `/opt/sentry-native`:

    curl -L https://github.com/constructpm/sentry-native-build/releases/download/v5.15.8/sentry-native-5.15.8-$PLATFORM.tar.gz | sudo tar -xJC /opt

where `$PLATFORM` is `ubuntu-18.04` or `ubuntu-22.04`.


## Details

Here's some more info about how this repo works. It's basically just a workflow that pulls the sentry-native source code for a specific version, builds it, and then optionally produces a release.

### The build

The workflow will pull the Qt source code from the `getsentry/sentry-native` github repo. 
We use the tags on this repo (eg `v6.7.1` or `v5.15.8`) when checking out to make sure we checkout a specific sentry-native version.
The sentry-native version specified at the top of the workflow file is this tag without the `v` prefix.

### Trigger the build

To trigger a build just create a PR and push to it. Every push will build sentry-native and provide the results as artefacts tied to the workflow run.

### Trigger the release

To trigger a release just push a tag that starts with `v`. Ideally push a tag matching the version of sentry-native you want to build a release for. The release will use the tag you provide to both tag and name the release (so tag `v1.2.3` will produce `Release v1.2.3` with tag `v1.2.3`).
Simply pushing a tag will trigger the build process (on any branch), and then will create a Release using the result of that build. That release can be used via the `curl` process listed above.

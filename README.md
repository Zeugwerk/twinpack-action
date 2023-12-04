# twinpack-action

This [GitHub Action](https://github.com/features/actions) can be used to upload a TwinCAT .library file as a package to [Twinpack](https://github.com/Zeugwerk/Twinpack), the first TwinCAT Package manager.
Usually you'd want to create workflow such that any release of your repository is automatically uploaded to the package manager and/or trigger the publish workflow manually.

Using Twinpack is **completely free** and the package manager is **open source**, but you have to [register an account](mailto:info@zeugwerk.at). This is needed so nobody can publish new versions of your packages under your name. Note that your `username` and `password` can be used with all other actions that are provided by Zeugwerk, namely

- [zkdoc-action](https://github.com/Zeugwerk/zkdoc-action) automatic documentation generation, can be used to generate up-to-date documentation, which is directly hosted on GitHub or on a private server
- [zkbuild-action](https://github.com/Zeugwerk/zkbuild-action) compiling and unittesting a PLC with a GitHub action. For Libraries the binary is uploaded to GitHub
- [zkbinding-action](https://github.com/Zeugwerk/zkbindings-action) code generation for a PLC for C++ and C# to automatically generate bindings for HMIs and other tools interacting with the PLC.

## Inputs

* `username`: (Required) Username of a Zeugwerk Useraccount 

* `password`: (Required) Password of a Zeugwerk Useraccount 

* `path`: (Optional) This defaults to "." and controls the path where the Twinpack command line interface is looking for .library files

* `configuration`: (Optional) Defaults to "Release" and should not be changed. For users without a subscription to our services the value can not be changed. It is used to upload debug builds and snapshots to Twinpack

* `target`: (Optional) Defaults to "TC3" and should not be changed. For users without a subscription to our services the value can not be changed. It is used to specify the target that a library was compiled for. This makes it possible to publish the same version of a library for different TwinCAT versions.

* `branch`: (Optional) Default to "main" and should not be changed. It can be used to upload temporary releases of feature branches for continuous integration

### Creating secrets

We highly recommend to store the value for `username` and `password` in GitHub as secrets. GitHub Secrets are encrypted and allow you to store sensitive information, such as access tokens, in your repository. Do these steps for `username` and `password`

1. On GitHub, navigate to the main page of the repository.
2. Under your repository name, click on the "Settings" tab.
3. In the left sidebar, click Secrets.
4. On the right bar, click on "Add a new secret" 
5. Type a name for your secret in the "Name" input box. (i.e. `ACTIONS_ZGWK_USERNAME`, `ACTIONS_ZGWK_PASSWORD`)
6. Type the value for your secret.
7. Click Add secret.


## Example usage

Save this content in your repository to `.github/workflows/publish.yml` to enable automatic uploading of a release to Twinpack whenever you publish a new release

```yaml
name: Publish
on:
  release:
    types: [published]
  workflow_dispatch:
jobs:
  Deploy:
    name: Twinpack
    runs-on: windows-latest
    steps:
      - uses: robinraju/release-downloader@v1.8
        with:
          latest: true
          fileName: "*.library"
      - uses: Zeugwerk/twinpack-action@v0.4.0
        with:
          username: ${{ secrets.ACTIONS_ZGWK_USERNAME }}
          password: ${{ secrets.ACTIONS_ZGWK_PASSWORD }} 
```

See the [struckig](https://github.com/stefanbesler/struckig/actions) project for a demonstration on how to use the action


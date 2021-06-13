# Quasar Electron Builder Action

This is a modified work of https://github.com/samuelmeuli/action-electron-builder so that it can support Quasar framework.

3. **Add a workflow file** to your project (e.g. `.github/workflows/build.yml`):

   ```yml
   name: Build/release

   on: push

   jobs:
     release:
       runs-on: ${{ matrix.os }}

       strategy:
         matrix:
           os: [macos-latest, ubuntu-latest, windows-latest]

       steps:
         - name: Check out Git repository
           uses: actions/checkout@v1

         - name: Install Node.js, NPM and Yarn
           uses: actions/setup-node@v1
           with:
             node-version: 10

         - name: Build/release Electron app
           uses: samuelmeuli/action-electron-builder@v1
           with:
             # GitHub token, automatically provided to the action
             # (No need to define this secret in the repo settings)
             github_token: ${{ secrets.github_token }}

             # If the commit is tagged with a version (e.g. "v1.0.0"),
             # release the app after building
             release: ${{ startsWith(github.ref, 'refs/tags/v') }}
   ```

## Usage

### Building

Using this the workflow above, GitHub will build your app every time you push a commit.

### Releasing

When you want to create a new release, follow these steps:

1. Update the version in your project's `package.json` file (e.g. `1.2.3`)
2. Commit that change (`git commit -am v1.2.3`)
3. Tag your commit (`git tag v1.2.3`). Make sure your tag name's format is `v*.*.*`. Your workflow will use this tag to detect when to create a release
4. Push your changes to GitHub (`git push && git push --tags`)

After building successfully, the action will publish your release artifacts. By default, a new release draft will be created on GitHub with download links for your app. If you want to change this behavior, have a look at the [`electron-builder` docs](https://www.electron.build).

## Configuration

### Options

You can configure the action further with the following options:

- `package_root`: Directory where NPM/Yarn commands should be run (default: `"."`)
- `build_script_name`: Name of the optional NPM build script which is executed before `electron-builder` (default: `"build"`)
- `skip_build`: Whether the action should execute the NPM build script before running `electron-builder`
- `use_vue_cli`: Whether to run `electron-builder` using the [Vue CLI plugin](https://nklayman.github.io/vue-cli-plugin-electron-builder) instead of calling the command directly
- `args`: Other arguments to pass to the `electron-builder` command, e.g. configuration overrides (default: `""`)
- `max_attempts`: Maximum number of attempts for completing the build and release step (default: `1`)

See [`action.yml`](./action.yml) for a list of all possible input variables.

# All other options are same as [action electron](https://github.com/samuelmeuli/action-electron-builder).

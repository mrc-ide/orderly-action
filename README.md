# orderly-action

This action allows you to use orderly from your GitHub workflow, and optionally
connect to a Packit instance.

## Usage

The following configuration will provide the basic flow for installing and
initializing orderly in your workflow:

```yaml
jobs:
  test:
    permissions:
      contents: read
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: r-lib/actions/setup-r@v2
        with:
          extra-repositories: https://mrc-ide.r-universe.dev
      - uses: mrc-ide/orderly-action@main
```

## Connecting to a Packit instance

> [!NOTE]
> Each GitHub repository must be manually authorised to access a Packit
> instance. Please talk to the RESIDE team if you are interested in using Packit
> from your GitHub workflow.

By passing in a `packit-url` parameter to the action, it will automatically add
it as a location. In order to allow the action to authenticate against Packit,
you must grant the job the `id-token: write` permission.

```yaml
jobs:
  test:
    permissions:
      contents: read
      # This is needed to authenticate to Packit
      id-token: write
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: r-lib/actions/setup-r@v2
        with:
          extra-repositories: https://mrc-ide.r-universe.dev
      - uses: mrc-ide/orderly-action@main
        with:
          # Replace this with your Packit instance URL.
          packit-url: https://packit.dide.ic.ac.uk/reside
```

By default, the location is added using `packit` as its name, although this can
be overriden by setting the `location-name` parameter.

From here on out, your workflow can pull and push packets from the Packit
instance. For example, by adding the following snippet the workflow would run
the `analysis` report from the repository and push the result of it to our
Packit instance:

```yaml
      - run: |
          id <- orderly2::orderly_run("analysis")
          orderly2::orderly_location_push(id, "packit")

        shell: Rscript {0}
```

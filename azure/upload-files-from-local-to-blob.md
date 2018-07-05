# Upload files from local file system to an Azure blob

Given that there is a blob named "assets" and a folder in the local system called "./static", to manually upload all the
files from this folder, do:

1. List all the files.
1. Run the [`az storage blob upload`](https://docs.microsoft.com/en-us/cli/azure/storage/blob?view=azure-cli-latest#az-storage-blob-upload) command.

The sequence of commands may then look like

```bash
cd assets
find . -type f -exec az storage blob upload --connection-string $AZURE_CONNECTION_STRING --container-name assets --name {} --file {} \;
```

Or, using short parameters:

```bash
cd assets
find . -type f -exec az storage blob upload --connection-string $AZURE_CONNECTION_STRING -c assets -n {} -f {} \;
```

Take note that the `--connection-string` parameter is unnecessary when the `$AZURE_CONNECTION_STRING` environment
variable is present.

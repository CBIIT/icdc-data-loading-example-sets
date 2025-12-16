# ICDC Data Loading Example Sets

This repository contains example data loading files for the Integrated Canine Data Commons (ICDC). These files demonstrate the proper format and structure for data submissions and can be downloaded through the ICDC Data Model Navigator.

## Repository Structure

```
icdc-data-loading-example-sets/
├── config.json                          # Configuration file for dynamic downloads
├── TCL01 Loading File Example Set/      # Example files directory
│   ├── TCL01-case.txt
│   ├── TCL01-demographic.txt
│   ├── TCL01-diagnosis.txt
│   ├── TCL01-enrollment.txt
│   ├── TCL01-principal_investigator.txt
│   ├── TCL01-publication.txt
│   ├── TCL01-sample.txt
│   ├── TCL01-study.txt
│   ├── TCL01-File_Transfer_Manifest - Sequence Analysis Files.tsv
│   └── TCL01-File_Transfer_Manifest - Study Files.tsv
└── README.md
```

## Configuration File (config.json)

The `config.json` file defines which files to include when using dynamic download mode:

```json
{
  "title": "ICDC_Data_Loading_Example_Files-TCL01",
  "loadingExamples": {
    "TCL01-case.txt": "https://raw.githubusercontent.com/CBIIT/icdc-data-loading-example-sets/main/TCL01%20Loading%20File%20Example%20Set/TCL01-case.txt",
    "TCL01-demographic.txt": "https://raw.githubusercontent.com/CBIIT/icdc-data-loading-example-sets/main/TCL01%20Loading%20File%20Example%20Set/TCL01-demographic.txt"
  }
}
```

### Fields

- **`title`**: Base name for the downloaded ZIP file (timestamp will be appended automatically)
- **`loadingExamples`**: Object mapping filenames to their raw GitHub URLs

### URL Requirements

- Use `raw.githubusercontent.com` URLs (not `github.com`)
- URL-encode spaces as `%20`
- Use the correct branch name (`main`)
- Include the full path from repository root

## Adding New Example Files

### 1. Add Files to Repository

```bash
# Create or update example files
git add "TCL01 Loading File Example Set/TCL01-new-node.txt"
git commit -m "Add new node example file"
```

### 2. Update config.json

Add the new file to the `loadingExamples` object:

```json
{
  "title": "ICDC_Data_Loading_Example_Files-TCL01",
  "loadingExamples": {
    ...existing entries...,
    "TCL01-new-node.txt": "https://raw.githubusercontent.com/CBIIT/icdc-data-loading-example-sets/main/TCL01%20Loading%20File%20Example%20Set/TCL01-new-node.txt"
  }
}
```

### 3. Commit and Push

```bash
git add config.json
git commit -m "Update config with new example file"
git push origin main
```

### 4. Verify

Test the URL directly:
```bash
curl "https://raw.githubusercontent.com/CBIIT/icdc-data-loading-example-sets/main/TCL01%20Loading%20File%20Example%20Set/TCL01-new-node.txt"
```

## File Naming Conventions

- **Data Loading Templates**: `{STUDY_ID}-{node_name}.txt`
  - Example: `TCL01-case.txt`, `TCL01-sample.txt`

- **File Manifests**: `{STUDY_ID}-File_Transfer_Manifest - {description}.tsv`
  - Example: `TCL01-File_Transfer_Manifest - Study Files.tsv`

## Testing

### Validate config.json

Ensure the JSON is valid:
```bash
curl https://raw.githubusercontent.com/CBIIT/icdc-data-loading-example-sets/main/config.json | jq
```

### Test File URLs

Verify each file is accessible:
```bash
# Test a specific file
curl "https://raw.githubusercontent.com/CBIIT/icdc-data-loading-example-sets/main/TCL01%20Loading%20File%20Example%20Set/TCL01-case.txt"

# Should return the file contents, not a 404 error
```

### Test All URLs in config.json

```bash
# Extract and test all URLs from config.json
curl -s https://raw.githubusercontent.com/CBIIT/icdc-data-loading-example-sets/main/config.json | \
  jq -r '.loadingExamples[]' | \
  xargs -I {} curl -f -s -o /dev/null -w "%{http_code} {}\n" {}
```

All URLs should return `200`.

## Integration with Data Model Navigator

The Data Model Navigator can use this repository in two ways:

### Dynamic Mode (Uses this repository)

When configured for dynamic mode, the DMN fetches `config.json` and downloads all listed files to create a ZIP on-the-fly.

### Static Mode (Default)

By default, the DMN uses a pre-built ZIP file from the `icdc-model-tool` repository. This repository's `config.json` is only used when explicitly configured for dynamic mode.

## Troubleshooting

### Files Not Accessible

**Problem**: Getting 404 errors for file URLs

**Solutions**:
- Verify files exist at the specified paths
- Check branch name is correct (`main` not `master`)
- Ensure proper URL encoding for spaces (`%20`)
- Use `raw.githubusercontent.com` URLs

### Invalid JSON

**Problem**: config.json fails to parse

**Solutions**:
- Validate JSON at [JSONLint](https://jsonlint.com/)
- Remove trailing commas
- Use double quotes only
- Ensure UTF-8 encoding

### Files Changed But Old Version Downloads

**Problem**: Updates not reflected in downloads

**Solutions**:
- Wait a few minutes for GitHub CDN cache to clear
- Hard refresh the DMN page (Ctrl+F5)
- Check that changes are pushed to the correct branch

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/add-examples`)
3. Add or update example files
4. Update `config.json` if adding/removing files
5. Test all URLs are accessible
6. Commit changes (`git commit -m 'Add example files for new node'`)
7. Push to branch (`git push origin feature/add-examples`)
8. Open a Pull Request

## Support

For issues with this repository:
- **File Issues**: https://github.com/CBIIT/icdc-data-loading-example-sets/issues
- **DMN Integration Issues**: https://github.com/CBIIT/Data-Model-Navigator/issues
- **ICDC Support**: icdc@nih.gov

## License

This project is in the public domain within the United States, and copyright and related rights in the work worldwide are waived through the [CC0 1.0 Universal public domain dedication](https://creativecommons.org/publicdomain/zero/1.0/).

# LLC Bulk Load: Input and Output files

## Outline

The Bulk Load application consumes and produces valid JSON files.

## Input

See `bulk-load-file.json` for the full schema.

### Input Filename Format
All files placed in the input directory should have filenames in the form:
`{migration_partner_code}_{date}_{time}_{batch_id}_{records}.json`

```
- migration_partner_code :: Alpha numeric, no symbols, and 3 characters long.
- date                   :: Numeric and 6 characters. DDMMYY:
                            DD :: 01 - 31
                            MM :: 01 - 12
                            YY :: 01 - 99
- time                   :: Numeric and 6 characters. HHMMSS:
                            HH :: 00 - 23
                            MM :: 00 - 59
                            SS :: 00 - 59
- batch_id               :: Alpha numeric, no symbols, and 1 or more characters long.
- records                :: Numeric and 1 or more characters long.
```

Any file that does not have a filename in this format will cause an error to be added to the error output.
The contents of a file with an invalid filename will be ignored.

### Input File Format
The input files should contain an array of JSON objects in the form:

```
[
  {
    "meta-data": {
      "action": "ADD", "VARY", "IMPORT" or "CANCEL"
    },
    "local-land-charge": {
      ... Existing local land charge definition
  }
]
```

The action specified determines which end point in the maintain-api will be called.
```
ADD    :: /local-land-charge
IMPORT :: /local-land-charge
VARY   :: /local-land-charge/local-land-charge-id
CANCEL :: /local-land-charge/local-land-charge-id
```

A JSON schema for the file format is included in `bulk-load-file.json`

### Bulk Load Validation
The bulk-load process validates against the register schema.

## Output
The Bulk Load application produces three output files which are all placed in the output directory; `correlation`,
`error`, and `summary`.

### Error
Contains the `migration_partner_code`, original JSON, and an error message for each local land charge record that was
rejected by the register import process.

### Correlation
Contains the `originating_authority_charge_identifier`, `migration_partner_code` and new `local-land-charge` (id) and 
`version-id` for each local land charge record successfully added to the register.

### Summary
Contains counts of;
- Records imported successfully to the register.
- Records rejected by the register import process.
- Total records processed.
- Total successful adds.
- Total successful cancels.
- Total successful varies.

### Filenames
Each of the output files has a name matching the input file prefixed with the type of the output.

```
Input:
1AA_010517_000000_1_1.json

Output:
error_1AA_010517_000000_1_1.json
summary_1AA_010517_000000_1_1.json
correlation_1AA_010517_000000_1_1.json
```


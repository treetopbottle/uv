* https://github.com/astral-sh/uv/issues/411
* https://github.com/astral-sh/uv/issues/1442

## Related code

See crates/uv/src/commands/pip/list.rs

`ListFormat::Json` then to a `serde_json::to_string()` of a `Vec<Entry>`. This is printed "inline".
```sh
cargo run -- pip list --format=json
```

`VersionFormat::Json` then `serde_json::to_string_pretty()`
```sh
cargo run -- self version --output-format=json
```

`PythonListFormat::Json` then `serde_json::to_string()`
```sh
cargo run -- python list --output-format=json
```

Either uses `writeln!(printer.stdout() ...` or `println!()`

## Plan

Should use to_string_pretty(), recently added one was to_string()

I want to add an option `--report` for `uv pip install`.

1. Disable the current printed output and instead print the plan (is there test for command output?)
2. Add the option to `PipInstallArgs` (does this have tests?)
3. Create JSON output for the Plan struct (very testable, needs example plan data)

Test with this

```sh
cargo run -- pip install --report --dry-run
```

### Optional

1. Return str from print functions so we can write regression tests
    - These are actually written with `uv_snapshot!()` macro
2. Make more consistent by creating a struct for the output, create a function to create that struct and a function to turn that struct into json or other output formats
3. Use --output-format=json or --output=json?

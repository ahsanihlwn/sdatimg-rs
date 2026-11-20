# sdatimg

A fast, versatile tool written in Rust for bidirectional conversion between Android block-based OTA images (`transfer.list` and `new.dat`) and partition images (sparse or raw), packaged in a single standalone binary.

## Key Features

- **sdat2img**: Unpack Android block-based OTA artifacts (`system.transfer.list` + `system.new.dat`) into partition images (raw or sparse). Supports transfer list format versions 1 through 4 (Android 5.0 Lollipop up to modern Android releases).
- **img2sdat**: Convert Android partition images (sparse image or raw block image) into block-based OTA packages (`.new.dat`, `.transfer.list`, `.patch.dat`). Features automatic input format detection and optional incremental OTA diff generation.
- **simg2img**: Decompress Android sparse images (`0xED26FF3A`) into raw disk images.
- **img2simg**: Compress raw partition images into Android sparse images.

## Building from Source

Ensure you have a working Rust toolchain installed, then run:

```bash
cargo build --release
```

The compiled binary will be placed at `target/release/sdatimg` (or `target/release/sdatimg.exe` on Windows).

---

## Usage Guide & Examples

### 1. `sdat2img` (Extract OTA .dat to .img)

Convert `system.transfer.list` and `system.new.dat` into a raw partition image `system.img`:

```bash
sdatimg sdat2img system.transfer.list system.new.dat system.img
```

If the output path is omitted, it defaults to `system.img`:

```bash
sdatimg sdat2img vendor.transfer.list vendor.new.dat
```

Additional options:
- `--sparse`: Generate an Android sparse image directly instead of a raw image.
- `--no-clobber`: Do not overwrite if the output image already exists.
- `-q, --quiet`: Suppress progress and status messages.

Example creating a sparse image directly:
```bash
sdatimg sdat2img system.transfer.list system.new.dat system.sparse.img --sparse
```

---

### 2. `img2sdat` (Convert .img to OTA .dat)

Convert a partition image (sparse or raw) into `.new.dat`, `.transfer.list`, and `.patch.dat`:

```bash
sdatimg img2sdat system.img -o output_dir -p system
```

Generated files inside `output_dir/`:
- `system.new.dat`
- `system.transfer.list`
- `system.patch.dat`

Additional options:
- `-o, --outdir <DIR>`: Output destination directory (default: current directory `.`).
- `-p, --prefix <NAME>`: Base name for generated files (default: `system`).
- `-v, --version <1..4>`: Transfer list format version (default: `4` for Android 7.0+).
- `--format <auto|sparse|raw>`: Force input format handling (default: `auto`).
- `--file-map <FILE>`: Optional path to a file-to-blocks mapping file.
- `--clobbered <RANGES>`: Range set of clobbered blocks (default: `0`).
- `--src <SRC_IMG>`: Source image for computing incremental OTA diffs.
- `-q, --quiet`: Suppress progress logs.

Example specifying transfer list version and custom prefix:
```bash
sdatimg img2sdat vendor.img -o out -v 4 -p vendor
```

---

### 3. `simg2img` (Sparse to Raw)

Extract an Android sparse image into an uncompressed raw image:

```bash
sdatimg simg2img system.sparse.img system.raw.img
```

---

### 4. `img2simg` (Raw to Sparse)

Pack an uncompressed raw partition image into an Android sparse image:

```bash
sdatimg img2simg system.raw.img system.sparse.img
```

---

## License

This project is licensed under the [MIT License](LICENSE).

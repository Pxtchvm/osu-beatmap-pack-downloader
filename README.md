# osu! Beatmap Pack Downloader

A powerful and user-friendly tool for automatically downloading osu! Beatmap Packs from the official website.

![Terminal Preview](https://i.ibb.co/Nd539wH9/screen.png)

## Features

- **Multi-threaded downloading**: Download multiple packs simultaneously
- **Smart URL detection**: Automatically detects the correct URL pattern for each pack
- **Resume capability**: Continue interrupted downloads where you left off
- **Download queue**: Efficiently manages the download order
- **Progress tracking**: Real-time download speed and progress display
- **Configuration persistence**: Remembers which packs you've already downloaded
- **Bandwidth control**: Optional download speed limiting
- **Comprehensive logging**: Detailed logs for troubleshooting

## Table of Contents

- [Installation](#installation)
  - [Using pip](#using-pip)
  - [Using uv (Recommended)](#using-uv-recommended)
- [Basic Usage](#basic-usage)
- [Advanced Usage](#advanced-usage)
- [Command-Line Options](#command-line-options)
- [Understanding the Output](#understanding-the-output)
- [Troubleshooting](#troubleshooting)
- [How It Works](#how-it-works)
- [FAQ](#faq)

## Installation

### Prerequisites

- Python 3.6 or higher
- Internet connection
- Basic understanding of command-line interfaces

### Using pip

1. **Install Python**: Make sure you have Python installed. If not, download and install it from [python.org](https://python.org).

2. **Download the script**: Save the `osu_downloader_advanced.py` script to your computer.

3. **Install required packages**:
   ```bash
   pip install requests tqdm
   ```

### Using uv (Recommended)

[uv](https://github.com/astral-sh/uv) is a faster, more reliable alternative to pip. Here's how to set up your environment with uv:

1. **Install uv**:
   ```bash
   # On macOS/Linux
   curl -sSf https://install.undefined.sh | python3 -

   # On Windows (PowerShell)
   powershell -c "Invoke-WebRequest -UseBasicParsing https://install.undefined.sh | python -"
   ```

2. **Create a virtual environment**:
   ```bash
   # Create a new directory for your project
   mkdir osu-downloader
   cd osu-downloader

   # Create and activate a virtual environment
   uv venv
   
   # On Windows
   .venv\Scripts\activate
   
   # On macOS/Linux
   source .venv/bin/activate
   ```

3. **Install required packages**:
   ```bash
   uv pip install requests tqdm
   ```

4. **Download the script**: Save the `osu_downloader_advanced.py` script to the project directory.

## Basic Usage

### Downloading a Range of Packs

To download packs #1580 through #1590:

```bash
python osu_downloader_advanced.py --start 1580 --end 1590
```

### Downloading Specific Packs

To download specific packs (e.g., #1586, #1587, and #1590):

```bash
python osu_downloader_advanced.py --packs 1586,1587,1590
```

### Specifying a Download Directory

By default, packs are saved to `./osu_packs`. To change this:

```bash
python osu_downloader_advanced.py --start 1580 --end 1590 --dir "D:\OsuBeatmaps"
```

## Advanced Usage

### Concurrent Downloads

Increase download speed by running multiple downloads simultaneously:

```bash
python osu_downloader_advanced.py --start 1580 --end 1590 --threads 5
```

### Adjusting Chunk Size

For faster downloads on high-speed connections, increase the chunk size:

```bash
python osu_downloader_advanced.py --start 1580 --end 1590 --chunk-size 16384
```

### Limiting Bandwidth

Limit download speed (useful when you need to use your network for other tasks):

```bash
python osu_downloader_advanced.py --start 1580 --end 1590 --bandwidth-limit 5.0
```
This will limit each thread to 5 MB/s (total bandwidth used will be `threads × limit`).

### Retry Failed Downloads

Resume any previously failed downloads:

```bash
python osu_downloader_advanced.py --retry-failed
```

### Disable Resuming

Start fresh downloads even if partial downloads exist:

```bash
python osu_downloader_advanced.py --start 1580 --end 1590 --no-resume
```

### Disable Delays

For maximum speed (use with caution as it might stress the server):

```bash
python osu_downloader_advanced.py --start 1580 --end 1590 --no-delay
```

### Debugging

Increase log verbosity for troubleshooting:

```bash
python osu_downloader_advanced.py --start 1580 --end 1590 --log-level DEBUG
```

## Command-Line Options

Here's the complete list of available options:

| Option | Description | Default |
|--------|-------------|---------|
| `--start` | Starting pack number | None |
| `--end` | Ending pack number (inclusive) | None |
| `--packs` | Comma-separated list of specific pack numbers | None |
| `--dir` | Directory to save the packs | `./osu_packs` |
| `--threads` | Number of concurrent downloads | 3 |
| `--chunk-size` | Download chunk size in bytes | 8192 |
| `--no-delay` | Disable random delay between downloads | False |
| `--no-resume` | Disable resuming of partial downloads | False |
| `--retry-failed` | Retry previously failed downloads | False |
| `--config` | Configuration file path | `osu_downloader_config.json` |
| `--bandwidth-limit` | Limit download bandwidth in MB/s per thread | None |
| `--log-level` | Set logging level (DEBUG, INFO, WARNING, ERROR) | INFO |

## Understanding the Output

When running the script, you'll see output similar to this:

```
Progress: 3/10 complete, 0 failed (30.0%) | Pack #1586: 65.2% at 15.4 MB/s, Pack #1587: 32.1% at 14.8 MB/s
```

This indicates:
- 3 out of 10 packs have been successfully downloaded
- 0 packs have failed to download
- 30% of the total download task is complete
- Pack #1586 is 65.2% downloaded at a speed of 15.4 MB/s
- Pack #1587 is 32.1% downloaded at a speed of 14.8 MB/s

A log file named `osu_downloader.log` will also be created in the same directory as the script, which contains detailed information about the download process.

## Troubleshooting

### Common Issues

#### "URL not found" for all URL patterns

This typically means the pack doesn't exist or has a different URL pattern. Check if the pack number is correct.

#### Slow Downloads

Try these solutions:
- Increase the number of threads (`--threads` option)
- Increase the chunk size (`--chunk-size` option)
- Disable delays between downloads (`--no-delay` option)

#### Downloads Keep Failing

Possible solutions:
- Check your internet connection
- Try with fewer threads (the server might be throttling)
- Enable resumable downloads (remove `--no-resume` if used)
- Check the log file for detailed error messages

#### "Permission denied" Errors

Make sure you have write permissions to the download directory.

### Reading Logs

The log file contains detailed information about each action the downloader takes. If you're having issues, check this file for clues. You can increase log verbosity with `--log-level DEBUG`.

## How It Works

The osu! Beatmap Pack Downloader works by:

1. **Parsing your command-line options** to determine which packs to download
2. **Creating a download queue** for all the requested packs
3. **Spawning worker threads** to process the download queue
4. **For each pack**:
   - Checking if it's already downloaded
   - Trying different URL patterns to find the correct one
   - Downloading the pack with progress tracking
   - Handling any errors that occur
5. **Saving configuration** to remember which packs were downloaded successfully

The downloader uses a `.part` file extension for files being downloaded. Once a download is complete, the file is renamed to its final name.

## FAQ

### Q: Is this tool official from osu!?

A: No, this is an unofficial tool created to help osu! players download beatmap packs more efficiently.

### Q: Will this get me banned from osu!?

A: The tool respects the server by adding delays between downloads and using proper headers. However, excessive downloading might be noticed by server administrators. Use responsibly.

### Q: Why does it download some packs but fail on others?

A: The URL patterns for packs can vary. The tool tries the most common patterns, but some packs might use a different pattern. Future versions might add support for more patterns.

### Q: Can I download all existing packs at once?

A: While technically possible, it's not recommended as it would put significant load on the server. Consider downloading in smaller batches.

### Q: Will this work on Mac/Linux?

A: Yes, the tool is compatible with any platform that supports Python 3.6+.

### Q: How can I see which packs I've already downloaded?

A: Check the `osu_downloader_config.json` file, which contains lists of completed and failed packs.

---

## Legal Notice

This tool is provided for educational purposes only. Please respect osu!'s terms of service and use this tool responsibly. Do not distribute downloaded content without permission.

## Credits

This tool was created to make downloading osu! beatmap packs more efficient for the community. osu! is the property of ppy Pty Ltd.

---

If you find this tool helpful, consider supporting osu! by becoming an [osu!supporter](https://osu.ppy.sh/home/support).
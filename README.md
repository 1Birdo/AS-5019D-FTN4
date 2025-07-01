# AS-5019D-FTN4
Firmware Extraction + IPMI Automation


<div align="center">

  <h2>📷 Preview</h2>
  
  <h3>File Structure:</h3>

  <div style="display: flex; flex-wrap: wrap; justify-content: center; gap: 10px;">
    <img src="https://github.com/user-attachments/assets/67d28afa-a1e6-4a06-9d7f-785484c285e2" width="45%" style="max-width: 300px;">
    <img src="https://github.com/user-attachments/assets/b4a38a5a-751d-441d-ab6a-f6d5f96fe2cf" width="45%" style="max-width: 300px;">
    <img src="https://github.com/user-attachments/assets/13aca79d-2448-44c2-b17a-a4c43aeae116" width="45%" style="max-width: 300px;">
  </div>
</div>

# README - Zlib Data Extraction and Decompression from Firmware

## Overview

This project involves extracting and decompressing Zlib compressed data from a binary firmware file. Using tools like `binwalk` and manual extraction methods, we identify compressed data segments and then use `zlib` decompression methods to extract their contents. The goal is to allow for easier extraction of firmware files and the ability to analyze the data within them.

## Tools Used

- **binwalk**: A tool used for analyzing and extracting data from binary files.
- **dd**: A tool used for low-level copying and extraction of specific data ranges from a file.
- **Python**: For decompressing the extracted Zlib data.
- **zlib-flate**: (Optional) A tool to decompress Zlib data if available.

## Steps for Extraction and Decompression

### 1. **Identifying Compressed Data Using binwalk**

Start by running `binwalk` on your firmware binary to locate compressed data segments:

```bash
binwalk -e BMC_M11AST2500_20230725_03.17.05_STDsp.bin
```

This will give an output similar to the following:

```
DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
120388        0x1D644         SHA256 hash constants, little endian
189972        0x2E614         CRC32 polynomial table, little endian
7366040       0x706598        Zlib compressed data, default compression
7368094       0x706D9E        Zlib compressed data, default compression
7368328       0x706E88        Zlib compressed data, default compression
```

### 2. **Extracting Compressed Data Using dd**

Once you have identified the locations of the Zlib compressed data, use `dd` to extract these sections from the binary file. For example:

```bash
dd if=BMC_M11AST2500_20230725_03.17.05_STDsp.bin of=compressed_data1.bin bs=1 skip=7366040 count=2048
dd if=BMC_M11AST2500_20230725_03.17.05_STDsp.bin of=compressed_data2.bin bs=1 skip=7368094 count=2048
dd if=BMC_M11AST2500_20230725_03.17.05_STDsp.bin of=compressed_data3.bin bs=1 skip=7368328 count=2048
```

### 3. **Decompressing the Extracted Data**

After extraction, you can decompress the Zlib data. You can use either `zlib-flate` or Python for this.

#### Using `zlib-flate`:

```bash
zlib-flate -uncompress < compressed_data1.bin > decompressed_data1.bin
```

#### Using Python:

```python
import zlib

with open("compressed_data1.bin", "rb") as f:
    compressed_data = f.read()
    decompressed_data = zlib.decompress(compressed_data)

with open("decompressed_data1.bin", "wb") as f:
    f.write(decompressed_data)
```

### 4. **Repeat for All Extracted Files**

Repeat the extraction and decompression process for all the identified compressed data segments.

## Conclusion

This process will allow you to extract and decompress Zlib compressed data from firmware images. By using `binwalk` for initial analysis, `dd` for extraction, and `zlib` for decompression, you can efficiently access and inspect the contents of firmware files.

## License

This project is open-source. Feel free to use and modify it for your own purposes.

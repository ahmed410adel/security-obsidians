

### Tools
1. `exiftool` to extract the metadata of a file
2. `strings`
3. `binwalk`
4. `steghide` extract files from another files
	1. `steghide extract -sf <filename>`
5. `hexedit` website to edit hex values in files (for corrupted files)
6. `zsteg` hidden message
7. `dd` for disk 

## 1. Scenario file to image
in this CTF i got a file but after decoding it using base64 it becomes image and i open the image and found the flag


# Notes:
- In steganography the hacker hides data on the least significant bit because it does not have a high impact on the image color
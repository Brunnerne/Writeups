# de-compressed (misc)

The ZIP file provided can be unzipped to find a readme, but inspecting the bytes or running `binwalk`, we see there is an additional `secret.txt` file.
This is likely hidden and not extracted correctly because it has been removed from the central directory present in all zip files at the end.

It can still, however, be recovered manually, which we simply used CyberChef for, using the operation "Extract Files".
This gives us a ZIP file which still cannot be unzipped directly, but which `binwalk` can extract `secret.txt` from.

Reading the contents of the file, we get some text with weird spacing in between - these show up with `cat`, but not in an editor, pointing at zero-width chars. We can confirm this with a hexdump and lookup some of the unicode byte values.

Unicode zero-width steganography is fairly well-known, and we upload the file here to extract the hidden contents:
https://330k.github.io/misc_tools/unicode_steganography.html

The extracted file shows "Disregard the README, I am still on the team." followed by the flag.

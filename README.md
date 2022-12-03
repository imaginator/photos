# photos management

## Validate files


exiftool -r -if 'not $Iptc:Caption-Abstract' -p 'ImageDescription missing in $directory/$filename'  /srv/photos/originals/1999

# fix bad digitization date
exiftool -r -overwrite_original '-alldates<datetimeoriginal' '-XMP:alldates<datetimeoriginal'  /srv/photos/originals/1999

# confirm 
exiftool -G0:1 -time:all /srv/photos/originals/1998/1998\ 12\ 2005\ 07\ 02\ London\ with\ Ulrika/DSCN0271.jpg 

# remove crap by setting to null
exiftool '-xmp-microsoft:all='
exiftool '-xmp-video:all='

# show field names
exiftool  -S /srv/photos/originals/2002/2002\ 03\ 2002\ 04\ Avrils\ Visit/DCP_6664.jpg

# compare timestamps
exiftool -p '$directory/$filename' -r -if '$DateTimeOriginal !~ $DateTimeDigitized' /srv/photos/originals/

# Kodak camera wierdness
exiftool -r -overwrite_original -makernotes:all= /srv/photos/originals/1999

# Exiftool find all photos Without "createdate" EXIF tag:
exiftool -p "$directory/$filename" -r -if "(not $createdate)" .

#Exiftool find all photos Without "datetimeoriginal" EXIF tag:
exiftool -p "$directory/$filename" -r -if "(not $datetimeoriginal)" .

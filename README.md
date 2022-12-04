## notes for myself.

### fields to care about

What we care about:

- Title of the photo (used to generate the Album). EG Cape Town Trip 2020
- Comments: Rarely used but the option to annotate a photo
- lat/long (where the photo was taken
- date: when it was taken `XMP-xmp:CreateDate`
`- email address in photos
- face data
see: https://github.com/exiftool/exiftool/blob/master/arg_files/exif2xmp.args 

### fields not to care about
- Do not use ITPC fileds
- Makernotes?

## Validate files

start with the real fuckups:
```
exiftool -r -p '$directory/$filename' -if '(not $exif:all)'        -ext jpg /srv/photos/originals
exiftool -r -p '$directory/$filename' -if '(not $datetimeoriginal)'-ext jpg  /srv/photos/originals
```

Files missing XMP data area
```
exiftool -r                     -p '$directory/$filename' -if 'not $xmp:all'                /srv/photos/originals
exiftool -r -overwrite_original -p '$directory/$filename' -if 'not $xmp:all' '-all>xmp:all' /srv/photos/originals
```

Sometimes DateTimeDigitized disagrees
```
# check for mismatches
# fix (AllDates only affects three tags, DateTimeOriginal, ModifyDate, and CreateDate.)

exiftool -r -if '$DateTimeOriginal !~ $DateTimeDigitized'  -p '$createdate $DateTimeDigitized $directory/$filename'  /srv/photos/originals/ 
exiftool -r -overwrite_original -p '$directory/$filename' -if '$DateTimeOriginal !~ $DateTimeDigitized' '-alldates<datetimeoriginal' '-XMP:alldates<datetimeoriginal'  /srv/photos/originals/1999
for videos this can be useful 
exiftool -r -overwrite_original -p '$directory/$filename' -if '$DateTimeOriginal !~ $DateTimeDigitized' '-alldates<xmp:datecreated' '-XMP:alldates<xmp:datecreated' '-xmp:HistoryWhen=' '-xmp:DateTimeDigitized<xmp:datecreated' /srv/photos/originals/
```

Find missing Subject
exiftool -r -if '(not $Title)' -p '$directory/$filename' -ext jpg /srv/photos/originals
Fix Videos missing their Title:
exiftool -r "-title<${directory;s(.*/)()}" DIR



Exiftool write current file name to Title and Comment EXIF fields:

exiftool -r -overwrite_original "-xpcomment<${filename" "-comment<${filename" "-title<${filename" "-xptitle<${filename" .


# useful for fixing video files:
exiftool -v  '-datetimeoriginal<${filename;$_=substr($_,0,15)}' '-createdate<${filename;$_=substr($_,0,15)}' '-FileCreateDate<${filename;$_=substr($_,0,15)}' '-FileModifyDate<${filename;$_=substr($_,0,15)}' '-MetaDataDate<${filename;$_=substr($_,0,15)}' '-ModifyDate<${filename;$_=substr($_,0,15)}' '-XMP:alldates<${filename;$_=substr($_,0,15)}' -ext jpg  /srv/photos/originals/2002/2002\ 12\ 28\ Dec\ 2012/2012-12-25\ 07\ 52\ 34-1.jpg 
```


exiftool -r -if 'not $Iptc:Caption-Abstract' -p 'ImageDescription missing in $directory/$filename'  /srv/photos/originals/1999

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


Exiftool add CreateDate Exif Property and Copy DateTimeOriginal Exif Property Value into It

Useful for if many pictures do not have the CreateDate exif-property, but do have the DateTimeOriginal exif-property. If you want the CreateDate exif-property to have the same value as the DateTimeOriginal exif property:

exiftool -r -overwrite_original -if '(not $createdate and $datetimeoriginal)' '-createdate<datetimeoriginal'   <your directory>

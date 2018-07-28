+++
categories = []
date = "2018-01-04T23:51:40-04:00"
image = "/uploads/sntPost.jpg"
tags = []
title = "Convert .snt File Extension to .txt File"
weight = 5
writer = "Lukas Herman"

+++
This should take between two and ten minutes and from this tutorial, you should gain a useful file as well as some knowledge of SQL and python.

If your not looking for a detailed tutorial, scroll to the bottom or Ctrl+f "steps:" then Enter

To preface this post, I have always run a windows pc and have wanted to transition to Linux for a long time, but am no longer putting it off. I am also a compulsive note taker and use the native windows sticky note app I wanted to save my notes in moving from windows to Linux and rather than copying each of the thirty or so notes I figured it would be more educational and possibly faster to get all the data at once.

To do this I did some research and found the file on my computer which houses the 'sticky notes' this file has a .snt file extension, which is sort of propriety and there isn't any converters or ways to quickly pull the data from that file. Here is a tutorial about properly parsing windows sticky notes to an ordinary text file.

Your user's windows sticky notes file is stored in the path:

```cmd
OSDISK(C:)/Users/myuser/AppData/Roaming/Microsoft/Sticky Notes/StickyNotes.snt
```

To get your sticky note data into a readable format, the first thing is to find the accompanying SQL file ("SQL (pronounced "ess-que-el") stands for Structured Query Language. SQL is used to communicate with a database"), which has the .sqlite file extension. This is because sticky notes are rendered from a SQL table. This is where your sticky notes data is actually stored, and on your machine, you should find a file named plum.sqlite in :

```cmd
OSDISK(C:)/Users/myuser/AppData/Local/Packages/Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe/LocalState/plum.sqlite
```

\*all commands from here on are in Linux command line

This command shows all tables created inside the SQL file:

```sh
sqlite3 plum.sqlite ".tables"
```

\*if you are getting an error, install sqlite3

You should get:

```sh
InkListItem       StorageChangeSet  StrokeMetadata    User            
Note              Stroke            UpgradedNote   
```

In analyzing the raw data from the plum.sqlite file, we can see that all important information is stored in the "note" table. So we want to pull all notes out of there, to do this, type:

```sql
sqlite3 plum.sqlite
SELECT * FROM Note;
```

In this you'll see something like:

```sh
...
\id=9c62dbeb-e7d5-4479-854c-3ba1a9d56a09 acm 281 dr dean
\id=507cd5bd-8a3f-4c55-a4fd-e477d8ff42f4 cs 4001
\id=20bcadcc-44b8-4a20-9ba4-971764bcc5ba CS 4081
\id=73f2e01f-f371-4407-a348-c8ea17894650 2081
\id=deeab47a-7746-4ee7-8882-17a097f1ae33 **We enjoy Tara at the Writing Center**
\id=27bac295-a807-4110-b245-6c3ce5d566e8 clemson 29631 zip|V1NERgMAAAABAAAAASwEAAAAAAAAGgEAAL0AAAAAAAA=|4c52a407-e2c2-4a3f-ad2b-4e32d9b18372|240.0|240.0|Yellow|9645472c-c740-4f78-af23-89bea99194c2|08cd79b8-8c3d-4b1e-9ca3-2a32aed8352d|0|0||636209814542615296|||636480426923236284||||||
...
```

Save this in a file (.txt is fine) or pipe it into this python program. We're going to save it for simplicity.

```sh
sqlite3 plum.sqlite "select * from Note;" > result.txt
```

* be sure to name it result.txt or to change the file name in the python program

We want to use python to scrape all notes pulled from the SQL file, so copy  [ this code](https://gist.githubusercontent.com/lherman-cs/c4e98c7c8003da832cd145519e7f9786/raw/895e7e6fd022c2795519a1c19ac1674a5ef388b3/scraping.py) then run it on the data pulled from the SQL table:

```cmd 
wget "https://blog.lherman.tk/data/scraping.py"
```

OR copy

```python3
import re

# Scrapping the data
notes = []
note = []

# Note here: I used "result.txt"
with open("result.txt") as i:
    for line in i:
        line = ' '.join(line.split()[1:])
        if '|' in line:
            line = line[:line.find('|')]

            note.append(line)
            notes.append('\
'.join(note))
            note = []
            continue

        note.append(line)


# Enumarate the notes and print them out
for i, note in enumerate(notes):
    print(" {}. ".format(i + 1).center(80, "="))
    print(notes[i])
    print()
```

Finally, run the python program:

```sh
python3 scraping.py 
```

This will output to the screen, but you probably want to put it into a file so run:

```sh
python3 scraping.py > notes.txt
```

This python code will give you all the notes, formatted:

```sh
====================================== 1. ======================================
acm 281 dr dean
cs 4001
CS 4081
2081
**We enjoy Tara at the Writing Center**
clemson 29631 zip

====================================== 2. ======================================
Netflix movies:
...
```

Steps:

1. get SQL file

```sh 
OSDISK(C:)/Users/myuser/AppData/Local/Packages/Microsoft.MicrosoftStickyNotes_8wekyb3d8bbwe/LocalState/plum.sqlite
```

1. get only pertinent data
2. get & run python program

```sh
sqlite3 plum.sqlite "select * from Note;" > result.txt
wget "https://blog.lherman.tk/data/scraping.py"
python3 scraping.py > notes.txt
rm result.txt
```
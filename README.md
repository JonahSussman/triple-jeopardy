# triple-jeopardy

This project is a series of Python 3 scripts to extract [Jeopardy!] clues from the [J! Archive] website and dump them into a SQLite database for use elsewhere.

The inspiration for this project (found [here](https://github.com/whymarrh/jeopardy-parser)) was made for Python 2 and the original creator is no longer mainatining it. This repository is a continuation of that parser, and hopefully adds more features.

Tested with SQLite 3.22.0 and Python 3.6.9

  [Jeopardy!]:http://www.jeopardy.com/
  [J! Archive]:http://j-archive.com/

# Quick start

You must have Python 3 and SQLite3 installed. If you are using Windows, I recommend installing the excellent [Windows Subsystem for Linux]. You can use version 1 or 2, I tend to use version 1 for the NTFS disk speed. 

The following quick start uses bash (MacOS or Linux). It creates a Python 3 virtual environment at the directory `~/venvs/triple-jeopardy` for cleanliness, though it's technically optional.

```bash
mkdir -p ~/venvs
python3 -m venv ~/venvs/triple-jeopardy
source ~/venvs/triple-jeopardy/bin/activate

git clone git://github.com/JonahSussman/triple-jeopardy.git
cd triple-jeopardy
pip install -r requirements.txt

python3 download.py
python3 parser.py
```

If you have opted to use a venv as detailed above, **make sure that before you run the scripts, you run** `source ~/venvs/triple-jeopardy/bin/activate` **first**. You'll know the venv is active if you see `(triple-jeopardy)` at the beginning of the command line.

  [Windows Subsystem for Linux]:https://docs.microsoft.com/en-us/windows/wsl/install-win10

# Querying the database

The sqlite database is stored in the `clues.db` file. I would recommend using an SQLite database viewer program like [SQLite Browser]. This makes querying much easier.

[SQLite Browser]:https://www.sqlitebrowser.org

The database is split into 5 tables:

| Table name        | What it holds                                          |
| ----------------- | ------------------------------------------------------ |
| `airdates`        | Air dates for the shows, indexed by game number        |
| `documents`       | Mappings from clue IDs to clue text and answers        |
| `categories`      | The categories                                         |
| `clues`           | Clue IDs with metadata (game number, round, and value) |
| `classifications` | Mappings from clue IDs to category IDs                 |

If you opt to not use a database viewer, the following commands are helpful.

To get all the clues along with their metadata:

```sql
SELECT clues.id, game, round, value, clue, answer
FROM clues
JOIN documents ON clues.id = documents.id
-- WHERE <expression>
;
```

To get the category that a clue is in, given a clue id:

```sql
SELECT clue_id, category
FROM classifications
JOIN categories ON category_id = categories.id
-- WHERE <expression>
;
```

To get everything (although it is better to pick and choose what you're looking for):

```sql
SELECT clues.id, clues.game, airdate, round, value, category, clue, answer
FROM clues
JOIN airdates ON clues.game = airdates.game
JOIN documents ON clues.id = documents.id
JOIN classifications ON clues.id = classifications.clue_id
JOIN categories ON classifications.category_id = categories.id
-- WHERE <expression>
;
```

# Licence

This software is released under the MIT License. See the [LICENSE.md](LICENSE.md) file for more information.
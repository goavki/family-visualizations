# Family Visualizations

TODO: Add content

## Updating the data

The data for the family visualizer is contained in `.json` files within the `json/` folder. This data can be automatically updated using the `scraper.py` script located in the `scrapers/` folder.

The scraper gets data for a specified family, which should be passed as an argument (`python3 [path]/scraper.py FAMILY`).

By default, the script gets the history of all languages and pairs in the family, as well as some other data used by the visualizer, for languages in the specified family. This data will be outputted to the `{lang}.json` files (history of each language and its pairs) as well as the `{family}_pairData.json` and `{family}_transducers.json`, which contain more generic data about each family (contributors, state, location, stems). The script can, however, also be run in "shallow" mode (`-s` or `--shallow`) to only get data for the `{family}_pairData.json` and `{family}_transducers.json` files, to avoid long runtimes.

To count contributions properly, the script uses a `.mailmap` file, to avoid commits made by the same person but under different names/emails being counted separately (e.g. commits by "John Doe" and by "J. Doe" would be counted separately). To update this file, the script has an "update mailmap" mode (`-u` or `--updatemailmap`) that outputs the emails that aren't on the `.mailmap` file, along with some data to help identify the authors. They must then be added manually, as the script can't figure out whether this committer is already in the file, but with a different email. Also, you could technically choose any name for a committer, but it is better to choose their GitHub username. If they don't have a GitHub account, the name they committed under most frequently should be chosen. If you are running this option solely to output the unknown committers, the option can be run along with shallow mode, as the only committers that are logged are those that would go in `{family}_transducers.json`, since the contributions are only counted for this file and this file is already updated with shallow mode.

Because of an issue with the GitHub API not working well with file renames, the script clones the repo for each language and pair (into the `scrapers/git-repos/` folder), if they're not already present and updates them if they are, and gets the data by running `git log` in it (which does output commits past renames), which is slow, but there is currently no other option to get the full history of a dictionary. To avoid being slower than necessary, the scraper ignores commits that are already in the files and only gets data for the ones that aren't. This scraper should be updated if either the GitHub API is updated to support history past file renames or if [the stats-service](https://github.com/apertium/apertium-stats-service/) is updated to support history (see [this issue](https://github.com/apertium/apertium-stats-service/issues/46)).

Notes:
- The scraper prints some info about what it's working on at the time. This can be avoided using the quiet option (`-q` or `--quiet`)
- Some commits are skipped because their stems can't be counted (because of a syntax error in the dictionary, in most cases). To print some info about these, use `-v` or `--verbose`.
- Occasionally, the script will return an exception saying that [the stats-service](https://github.com/apertium/apertium-stats-service/) is updating, either in the `monoData` or `pairData` function. While it's updating, not all the necessary data is available. If this happens, please try again later (or run the script for another family, as the stats-service updates progressively).
- The data about which language is in each family is in `scrapers/families.json` and when Apertium starts supporting a new language, it should be added manually to the file.
- The `scrapers/iso3to1.json` allows the script to convert the language codes from iso639-3 standards to iso639-1 standards. This is useful for file renames, as most renames were to convert the file name from iso639-1 to iso639-3 standards. The script relies on _all_ languages needed being there, including the ones that don't have an iso639-1 code, which then just convert to themselves. If a language is, for some reason, not present on the file it should be added.
- If run on Windows, `git` could give such errors: `error: unable to create file tests/morphotactics/prefixes/PRN.*: No such file or directory` and then `fatal: unable to checkout working tree'`. This causes no issues for the script and is due to [this issue](https://github.com/apertium/organisation/issues/11)
- The scripts contained within the `scrapers/svn-old` folder are the old scrapers that got the data from the svn repo. They shouldn't be run anymore.

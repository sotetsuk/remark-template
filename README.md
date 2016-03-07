# Slides

## How to create new slide

1. Create new branch ```$ git checkout -b new_slide``` from **template branch**.
2. Rename folder: ```$ git mv template new_slide```.
3. Edit ```slide.md```, and confirm at local machine (see ```Local development```).
4. Commit and push branch to GitHub. 
5. Create pull request to ```gh-pages``` branch (default)
6. Confirm that the new slide is hosted at ```http://www.sotetsuk.net/slides/new_slide```.

## Formatting
See [remark wiki](https://github.com/gnab/remark/wiki) for formatting rules.

## Local development

Run server in the directory of your index.html file:

- Python 2.x: ```$ python -m SimpleHTTPServer 8000```
- Python 3.x: ```$ python -m http.server 8000```

and then, open ```http://localhost:8000```.

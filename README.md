# Slides

## How to create new slide

1. ```make new``` 
2. Create pull request to ```gh-pages``` branch (default)
3. Confirm that the new slide is hosted at ```http://www.sotetsuk.net/slides/new_slide```.

## How to update template

1. Edit files at template branch.
2. Commit and push.
3. Create pull request to ```gh-pages``` branch (default).

## Formatting
See [remark wiki](https://github.com/gnab/remark/wiki) for formatting rules.

## Local development

Run server in the directory of your index.html file:

- Python 2.x: ```$ python -m SimpleHTTPServer 8000```
- Python 3.x: ```$ python -m http.server 8000```

and then, open ```http://localhost:8000```.

## Mathjax

Do not forget to use back-quote e.g.: 

```
`$$\sum_{i=1}^n \lVert a_i \Vert_\infty$$`
```

This example is from [this repository](https://github.com/hamukazu/remarkjs_hamukazu).

## LICENSE
The slides without any license are provieded by MIT license.

# RaRCtf: Lemonthinker (100 Points)

generate your lemonthinks here!

![Lemonthink Home](home.jpg)

Note: All characters that look like a O are actually a 0, please  try replacing all O's with  0's if you find that your flag does not work.

## Analysing the source files

We can see from the files that the application is running on a `python flask server` and from the `Dockerfile` that the `flag.txt` is running in the root of the container. We need to somehow extract `flag.txt`.

On further inspection of `app.py`, we can see that `generate.py` is run using `os.system` with user inputs, which can possibly suggest a code injection method. The following lines suggests so:

```python
text = request.form.getlist('text')[0]
text = text.replace("\"", "")
filename = "".join(random.choices(chars,k=8)) + ".png"
os.system(f"python3 generate.py {filename} \"{text}\"")
```

We know that raw user input is being passed into `os.system`, so we can try to run commands. Through the Dockerfile, we know that we are running on a linux based environment through `python:alpine`, hence we can start trying `bash` commands.

We also notice that we cannot use `"` as it is being escaped by the replace line.

## Brute forcing?

From here, I tried several methods to have my input parsed as command line functions, such as `&` and `'` but to no avail. However, trying the back quote `` ` `` character, it seemed to allow me to pass in a command. For reference, here is the site when I key in `` `whoami` ``.

![whoami](whoami.jpg)

We can try to `cat` the flag out by using `` `cat ../flag.txt` `` but we end up running into a problem since the code prevents this (the flag contains the keyword that filters the flag out):

```python
if "rarctf" in text: # Don't try and exfiltrate flags from here :lemonthink:
  img = Image.open("static/generator/noleek.png") 
  img.save(f"static/images/{outfile}"
```

I also tried to copy or cat the files to another file but the write permissions are possibly not given so I end up hitting a dead end.

The way I solved it was to then use the pipe function with `cut` to remove the `rarctf` portion of the `flag` that had been `cat`.

Running `` `cat ../flag.txt | cut -c 4-` `` gives us the following:

![cat1](static/cat_1.png)

Repeating this process further through shifting the cut value allows us to retrieve the flag in its entirety to be:

`rarctf{b451c-c0mm4nd_1nj3ct10n_f0r-y0u_4nd_y0ur-l3m0nth1nk3rs_d8d21128bf}`

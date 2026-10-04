> **Coursework track · CIS 9760 Big Data Technologies · Module 4**
> Tag: 🛡️ **Portfolio-Defense** — containerization, Dockerfile authoring, REST-API integration,
> and secret management via environment variables directly patch the §12.2 deployment/prototype→
> production gap. Companion index: [coursework-README](./../coursework-README.md).
> *Original journal below, authored and submitted 29 Sep 2026 — preserved verbatim.*

---

# CIS 9760 Module 4 — Containerization, Docker, and a Custom API Application

**Big Data Technologies · Baruch College · Prof. Ecem Basak · Fall 2026**
Lectures: *Containerization*, *Docker* · Exercises: Docker on EC2, Currency Converter
Homework: **Exploring API Integration with Python** — submitted 29 Sep 2026

Built from nothing in one evening: a containerised Python application that queries a live REST
API, performs a computation on the result, and exposes a command-line interface. Six working
versions, each one tested before the next was started.

---

## Part 1 — What containerization is actually for

The lecture opened with four reasons, and the fourth is the one that reframes the other three.

| Reason | What it means |
|---|---|
| Repeatable results | An analysis nobody else can reproduce doesn't count |
| Run code anywhere | Written locally, runs in the cloud, behaves the same |
| Transport environments | Kills "it works on my machine" |
| **Packaging > implementation** | **The ability to package an environment matters more than what's inside it** |

That last line is the thesis. The skill being taught is not Python and not Docker specifically —
it's the discipline of making an environment portable, which outlives whichever tool implements it.

### Virtual machines versus containers

A **virtual machine** simulates a whole computer inside another computer: its own operating
system, its own applications, managed by a **hypervisor** that allocates hardware between guests.
Full isolation, at the cost of carrying an entire OS per instance.

A **container** virtualizes only the operating system. The hardware and the OS kernel are shared
with the host; what gets isolated is user space. Each application gets its own container, many
containers share one host.

| | Virtual machine | Container |
|---|---|---|
| Includes | full guest OS per instance | app and libraries only |
| Sits on | hypervisor | container engine, sharing the host kernel |
| Isolation | total hardware isolation | isolated user space |
| Cost | heavy | lightweight |
| Startup | slow | fast |

**AWS shows both models side by side.** EC2 with HVM is *full* virtualization — a complete
virtualized hardware stack, which is why you can run an unmodified custom OS on it. Lambda is
*partial* or API-level — it virtualizes the execution environment rather than emulating hardware,
which is why it starts almost instantly.

That contrast explains the whole course's infrastructure: the EC2 instance is a virtual machine,
and everything we ran inside it was a container.

---

## Part 2 — Docker, via cake

Basak's analogy is better than it first sounds, because it maps one-to-one onto the commands.

| Docker concept | Analogy | What it actually is |
|---|---|---|
| **Dockerfile** | the recipe | a text file of instructions |
| **Layer** | each ingredient and step | one instruction's worth of change |
| **Image** | the sealed kit | an immutable blueprint of the environment |
| **Container** | the baked cake | a running instance; many from one image |
| **Registry** | the library of recipes | where images are shared (e.g. DockerHub) |

The distinction that matters in practice is **image versus container**. An image is a frozen
snapshot. A container is one live run of it. You can bake many cakes from one kit — and crucially,
**changing the recipe doesn't change a cake you already baked.**

### Dockerfile structure

Execution runs top to bottom: comments, then a required `FROM` giving the base image, then
everything else.

Mine was four lines:

```dockerfile
FROM python:3.9
RUN pip install requests
COPY main.py .
ENTRYPOINT ["python", "main.py"]
```

| Line | Does |
|---|---|
| `FROM python:3.9` | base image — Python already installed |
| `RUN pip install requests` | the one dependency that isn't standard library |
| `COPY main.py .` | puts the script inside the image |
| `ENTRYPOINT ["python", "main.py"]` | runs it, **appending** anything after the image name as arguments |

That last line is what makes `docker run image --city1=Cairo` deliver `--city1=Cairo` to argparse.

### Layers and caching

Each instruction creates a distinct, read-only layer, and Docker rebuilds only the layers that
changed. That's why the first `docker build` took two minutes — pulling the Python base image,
installing `requests` — and every later one took seconds. Only the `COPY main.py` layer was
invalidated by my edits.

### Architecture

The **client** is what you type into. The **daemon** lives on the host, receives those commands,
and does the actual work of building images and creating containers. `docker build` doesn't build
anything itself; it hands the instruction to the daemon.

---

## Part 3 — The application

**City Weather Comparator.** Two city names in; current temperature for each; the difference
between them; which one is warmer. Celsius or Fahrenheit.

**API:** OpenWeatherMap, Current Weather Data (`/data/2.5/weather`), free tier.

The assignment required the script to *"not just print the output but also perform at least one
action with the retrieved data."* This does two: an arithmetic result that doesn't exist in the
response, and a comparison.

### The response shape

```json
{"coord":{...},
 "weather":[{"id":804,"main":"Clouds","description":"overcast clouds"}],
 "main":{"temp":16.71,"feels_like":16.79,"temp_min":15.95,"temp_max":17.36,...},
 "sys":{"country":"GB",...},
 "name":"London","cod":200}
```

The field I needed sits two levels down: `r.json()["main"]["temp"]`.

Three things worth recording about that structure:

- **`weather` is a list before it's a dict.** `weather[0]["description"]`. Forget the index and
  you get `KeyError: 0`
- **`sys` here is the API's key name**, nothing to do with Python's `sys` module. Same three
  letters, unrelated things — this genuinely confused me
- **Units are a request parameter, not a response field.** `metric` = °C, `imperial` = °F, and
  omitting it gives **Kelvin**

---

## Part 4 — Six layers

Each layer was built, run, and confirmed before the next was started. Nothing was written ahead of
a test.

| # | Added | Taught |
|---|---|---|
| 1 | Hardcoded city and key, print raw JSON | the toolchain works end to end |
| 2 | Extract `main.temp` | nested dictionary access |
| 3 | Two cities via `sys.argv`, one reusable function | define once, call twice |
| 4 | Difference and comparison | the required "action" |
| 5 | Key moved to `os.environ` | secrets belong outside code |
| 6 | argparse with `--city1`, `--city2`, `--units` | a real command-line interface |

The value of the layering showed up every time something broke: the failure was always in the
twenty lines I'd just touched, never anywhere else.

### Final script

```python
from requests import get
import argparse
import sys
import os


def get_temp(city, units):
    key = os.environ["API_KEY"]
    r = get(f"https://api.openweathermap.org/data/2.5/weather?q={city}&appid={key}&units={units}")
    return r.json()["main"]["temp"]


if __name__ == '__main__':
    parser = argparse.ArgumentParser(
        description='Compare the current temperature in two cities using the OpenWeatherMap API.')

    parser.add_argument('--city1', type=str, help='First city to compare (e.g., Reykjavik)', required=True)
    parser.add_argument('--city2', type=str, help='Second city to compare (e.g., Cairo)', required=True)
    parser.add_argument('--units', type=str, help='metric for Celsius, imperial for Fahrenheit',
                        default='metric', choices=['metric', 'imperial'])

    args = parser.parse_args(sys.argv[1:])

    city1 = args.city1
    city2 = args.city2
    units = args.units

    symbol = "C" if units == "metric" else "F"

    temp1 = get_temp(city1, units)
    temp2 = get_temp(city2, units)

    if temp1 > temp2:
        warmer_city = city1
        cooler_city = city2
    else:
        warmer_city = city2
        cooler_city = city1

    temp_difference = abs(temp1 - temp2)

    print(f"{city1} is {temp1}°{symbol} and {city2} is {temp2}°{symbol}.")
    print(f"{warmer_city} is warmer than {cooler_city} by {temp_difference:.1f}°{symbol}.")
```

No API key anywhere in the file.

### Output

```
$ docker run -e API_KEY=<key> weather_app:1.0 --city1=Reykjavik --city2=Cairo
Reykjavik is 7.81°C and Cairo is 23.42°C.
Cairo is warmer than Reykjavik by 15.6°C.

$ docker run -e API_KEY=<key> weather_app:1.0 --city1=Reykjavik --city2=Cairo --units=imperial
Reykjavik is 46.06°F and Cairo is 74.16°F.
Cairo is warmer than Reykjavik by 28.1°F.
```

### How I proved it was actually correct

Not by looking at it. By checking the arithmetic:

| | °C | °F | (C × 9/5) + 32 |
|---|---|---|---|
| Reykjavik | 7.81 | 46.06 | 46.058 ✓ |
| Cairo | 23.42 | 74.16 | 74.156 ✓ |

And the **difference** converts differently from the temperatures: 15.61 × 9/5 = 28.10, with no
`+ 32`, because a *gap* of 15.61°C is a gap of 28.1°F while a *temperature* of 15.61°C is 60.1°F.
Output said 28.1.

That's the proof the `units` parameter reached the API. Had I only changed the display label, the
Fahrenheit run would have shown 7.81 and 23.42 with an F attached — plausible, and wrong by
twenty-five degrees.

**Verifying by relationship rather than by appearance is the transferable habit here.**

---

## Part 5 — Everything that went wrong

The actual content of the evening.

### 1. `docker: invalid reference format`

The File Browser startup command, pasted as usual across several lines with `\` continuations,
suddenly failed.

A `\` must be the **very last character on its line**. One invisible trailing space and the
backslash escapes the space instead of the newline; the command terminates early and Docker gets
nothing valid where the image name should be.

**Fix:** run it as a single line. The continuations were never doing anything but making it pretty.

**Third time whitespace has cost me this semester** — after `rm -r .. /SamllApplications`, where
a stray space nearly deleted my home directory, and an unquoted `git config user.name`. Whitespace
is syntax in a shell, not formatting.

### 2. A Markdown link inside a URL string

```python
r = get(f"[https://api.openweathermap.org/...](https://api.openweathermap.org/...)")
```

From copying a *rendered* hyperlink rather than plain text. Python would have sent the brackets
and all.

Related and worse: I later tried to redact my key by writing `KEY_BLUR` as the link **text** —
but in `[text](url)` only the text displays while the url travels intact. **A redacted-looking
link is not a redacted link.** Type the placeholder characters directly instead.

### 3. `IndentationError` on a function containing only comments

```python
def get_temp(city):
    # build the URL
    # get() it
    # return the temperature
```

**Comments are invisible to Python.** A `def` needs at least one real statement; as far as the
parser was concerned this function had an empty body. The comments were placeholders to *replace*,
and I'd kept them.

### 4. `return` outside a function

I put the function's body inside the `if __name__` block instead. **In Python, indentation *is*
the structure** — there are no braces, so what a line belongs to is decided entirely by how far
it's indented. Two errors at once: the function was still empty, and `return` is illegal outside
one.

Also a scope lesson: `city` only exists *inside* `get_temp`, as its parameter. Out in `__main__`
there is no `city` — only `city1` and `city2`.

### 5. Naming variables after example values

```python
London = sys.argv[1]
Paris = sys.argv[2]
```

Run it with Tokyo and you'd have a variable called `London` holding `"Tokyo"`. **A variable name
describes the slot, not one value that might go in it.**

### 6. Using a variable before creating it

Added `symbol = "C" if units == "metric" else "F"` before the argparse block that creates `units`.
Python reads top to bottom; a name must appear on the left of an `=` before anything reads it.

### 7. The silent one — `units` accepted but ignored

```python
def get_temp(city, units):
    r = get(f"...&units=metric")   # ← hardcoded
```

The function took a `units` parameter and never used it. `--units=imperial` would have set the
label to `F` while the API kept returning Celsius: **7.81°F for Reykjavik.** No error, no warning,
a number that looks entirely reasonable.

Third instance this semester of *ran clean, answer wrong* — after silent vector recycling in R and
December vanishing off a chart without complaint. None of the three produced a message.

### 8. Replacing a constant in one place and not the other

Swapped the hardcoded `°C` for `{symbol}` in the second print statement and missed the first.
Output would have labelled the same run C in one sentence and F in the next.

**When you replace a hardcoded value with a variable, search for every occurrence.** One missed
spot produces internally inconsistent output, which is worse than being uniformly wrong.

### 9. Copy-pasted help text that described the wrong application

The argparse block started life as the currency converter's. The description still said *"using
ExchangeRate-API"* and `--city1`'s help still read *"(e.g., USD)"*.

Those aren't comments — **`--help` prints them.** A weather tool whose own documentation talks
about currency codes reads as unreviewed.

---

## Part 6 — Concepts that stuck

**Define once, call twice.** Layer 3 needed the fetch-and-extract logic for two cities. Writing it
as a function meant layer 4 operated on two clean values and layer 6 barely touched the logic. The
copy-paste version would have needed every later change made in two places.

**`return` versus `print`.** `print` displays a value and discards it; `return` hands it back so
you can compute with it. Layer 4 needed the value, not the display.

**f-strings.** The `f` prefix only matters when there are `{placeholders}`. By the end the URL had
three — `{city}`, `{key}`, `{units}` — all substituted at call time.

**Environment variables for secrets.** `os.environ["API_KEY"]` in the code, `-e API_KEY=...` on
the command line. The key never enters the file, so the file is safe to share. And `os.environ[...]`
raising `KeyError` when unset is the *right* behaviour — a loud immediate failure beats a request
going out with an empty key and coming back 401.

**`-e` goes before the image name.** Everything before the image is an instruction to Docker;
everything after it is passed to the application. That boundary is the whole reason `ENTRYPOINT`
works the way it does.

**`default=` and `required=True` are opposites**, and `choices=` turns a typo into a readable error
instead of a confusing API failure.

**`if` as a value.** `symbol = "C" if units == "metric" else "F"` — an expression that produces a
value rather than a branch that executes. Same idea as `punct <- if(emphasis) "!" else ""` in R
the week before. Different syntax, identical concept.

**Floating point, again.** `abs(23.42 - 7.81)` gives `15.610000000000001`. `{:.1f}` fixes the
display without touching the value. Same phenomenon as `sqrt(3)^2 != 3` in R — a printed value is
not the value.

---

## Part 7 — The one that would have got me

The Docker exercise spends a full page on it, and it's the headline risk of the whole module:

> *"Docker uses the image, which contains a snapshot of main.py at the time it was built. If
> main.py was part of the image when it was created, Docker will execute that version, even if
> you've since updated main.py."*

Edit the script, skip `docker build`, run the container, and you see your **old** output. No
error. No warning. The natural conclusion is that your fix didn't work.

It's the same failure shape as the hardcoded `units` and the silent recycling in R: the system
runs perfectly and gives you the wrong answer. **Rebuild after every single edit** — it costs
seconds and removes the possibility entirely.

---

## Part 8 — What carries forward

**Technical**

- Docker build/run cycle, and the image-is-a-snapshot model
- `ENTRYPOINT` exec form, and how arguments reach the application
- Layer caching — why the first build is slow and the rest aren't
- Environment variables as the mechanism for anything secret
- argparse with defaults and constrained choices
- Reading nested JSON, and checking response shape before writing code against it

**Method**

- **Build in layers and test each one.** Six working versions beat one broken one, and every
  failure was localised to what I'd just touched
- **Verify by relationship, not appearance.** The 9/5 check proved correctness in a way that
  reading the output could not
- **Read the API's actual response before writing code.** Fifteen minutes in a browser saved an
  hour of `KeyError`
- **When you swap a constant for a variable, find every occurrence**
- **Whitespace is syntax**
- **Watch for the silences.** Three separate bugs this semester ran cleanly and produced wrong
  answers. Only deliberate verification catches those

**Next in this course**

OpenSearch (6 Oct), EMR (13 Oct), Kinesis streaming (Nov), Athena + Glue (Nov). Every one of them
runs on the same EC2 instance, through the same container discipline. The startup sequence and the
build-test-rebuild rhythm are now routine, which is the point of Module 4 sitting where it does.

---

*Submitted 29 September 2026, 1:03 AM — 23 hours before deadline. API key redacted throughout;
the live key exists only in the submitted README and is scheduled for regeneration after grading.*

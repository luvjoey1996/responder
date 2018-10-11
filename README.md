# Responder: a Sorta Familar HTTP Framework for Python

![](https://farm2.staticflickr.com/1937/30196007887_604e2f10d8_k_d.jpg)

I'm adept to keep the "for humans" tagline off this project, until it comes out of the prototyping phase. I'm building this to learn, and to have fun -- while, at the same time, trying to bring something new to the table.

The Python world certianly doesn't need more web frameworks. But, it does need more creativity, so I thought I'd bring some of my ideas to the table and see what I could come up with.

## But will it blend?

```python
import responder

api = responder.API()

@api.route("/{greeting}")
def greet_world(req, resp, *, greeting):
    resp.text = f"{greeting}, world!"
    resp.headers.update({'X-Life': '42'})
    resp.status_code = api.status_codes.HTTP_416

if __name__ == '__main__':
    api.run()
```

This gets you a WSGI app, with WhiteNoise pre-installed, jinja2 templating (without additional imports), and a production webserver (ready for slowloris attacks), serving up requests with gzip compression automatically.

Class-based views:

```python
@api.route("/{greeting}")
class GreetingResource
def on_request(req, resp, *, greeting):   # or on_get...
    resp.text = f"{greeting}, world!"
    resp.headers.update({'X-Life': '42'})
    resp.status_code = api.status_codes.HTTP_416
```

# The Basic Idea

The primary concept here is to bring the nicities that are brought forth from both Flask and Falcon and unify them into a single framework, along with some new ideas I have. I also wanted to take some of the API primitaves that are instilled in the Requests library and put them into a web framework. So, you'll find a lot of parallels here with Requests:

- Setting `resp.text` sends back unicode, while setting `resp.content` sends back bytes.
- Setting `resp.media` sends back JSON/YAML (`.text`/`.content` override this).
- Case-insensitive `req.headers` (from Requests directly).
- `resp.status_code`, `req.method`, `req.url`, and other familar friends.


## Old Ideas

- Flask-style route expression, with new capabilities -- primarily, the ability to cast a parameter to integers as well as other types that are missing from Flask, all while using Python 3.6+'s new f-string syntax.

- I love Falcon's "every request and response is passed into to each view and mutated" methodology, especially `response.media`, and have used it here. In addition to supporting JSON, I have decided to support YAML as well, as Kubernetes is slowly taking over the world, and it uses YAML for all the things. Content-negotiation and all that.

## New Ideas

- **A built in testing client that uses the actual Requests you know and love**.
- The ability to mount other WSGI apps easily.
- Automatic gzipped-responses (still working on that).
- In addition to Falcon's `on_get`, `on_post`, etc methods, Responder features an `on_request` method, which gets called on every type of request, much like Requests.
- WhiteNoise is built-in, for serving static files (this has yet to be built out, there's no templating or `static_url` yet)
- Waitress built-in as a production web server. I would have chosen Gunicorn, but it doesn't run on Windows. Plus, Waitress serves well to protect against slowloris attacks, making nginx unneccessary in production.
- GraphQL support, via Graphene. The goal here is to eventually have an embedded version of GraphiQL exposable at any route.

## Future Ideas

- I want to be able to "mount" any WSGI app into a sub-route.
- Cooke-based sessions are currently an afterthrought, as this is an API framework, but websites are APIs too.
- Potentially support ASGI instead of WSGI. Will the tradeoffs be worth it? This is a question to ask. Procedural code works well for 90% use cases.
- If frontend websites are supported, provide an official way to run webpack.

# The Goal

The primary goal here is to learn, not to get adoption. Though, who knows how these things will pan out.

# When can I use it?

When it's ready. It's not. I started work on this a few days ago. It works surprisingly well, considering! :)

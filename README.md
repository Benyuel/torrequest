
It's basically a wrapper around [Stem](https://stem.torproject.org) and
[Requests](http://docs.python-requests.org/en/master/) libraries.

## Dependencies
You need Tor. It's available via Homebrew.
```sh
brew install tor
```

After installation, you may want to configure Tor by creating a `.torrc` file in your `$HOME` directory. More information is available on [Tor
documentation](https://www.torproject.org/docs/tor-manual.html.en).

example `~/Library/Application\ Support/TorBrowser-Data/Tor/torrc`
```
DataDirectory /Applications/TorBrowser.app/TorBrowser/Data/Tor
GeoIPFile /Applications/TorBrowser.app/TorBrowser/Data/Tor/geoip
GeoIPv6File /Applications/TorBrowser.app/TorBrowser/Data/Tor/geoip6
HiddenServiceStatistics 0
# only for US nodes
ExitNodes {us}
StrictNodes 1
```

## Examples
```python
from bs4 import BeautifulSoup
from torrequest import TorRequest
import http.cookiejar
from http.cookiejar import LWPCookieJar
with TorRequest(proxy_port=9150, ctrl_port=9151, password=None) as tr:
    page = tr.get(first_page).content
    # to reset your traversal nodes used
    tr.reset_identity()
    c = LWPCookieJar()
    tr.session.cookies.update(c)
    # return clean soup of the page
    return BeautifulSoup(page)
```


```python
from torrequest import TorRequest

# Choose a proxy port, a control port, and a password. 
# Defaults are 9050, 9051, and None respectively. 
# If there is already a Tor process listening the specified 
# ports, TorRequest will use that one. 
# Otherwise, it will create a new Tor process, 
# and terminate it at the end.
with TorRequest(proxy_port=9050, ctrl_port=9051, password=None) as tr:

  # Specify HTTP verb and url.
  resp = tr.get('http://google.com')
  print(resp.text)

  # Send data. Use basic authentication.
  resp = tr.post('https://api.example.com', 
    data={'foo': 'bar'}, auth=('user', 'pass'))'
  print(resp.json)

  # Reset your Tor identity whenever you want. 
  # This is likely to change your IP address.
  tr.reset_identity()

  # TorRequest object also exposes the underlying Stem controller 
  # and Requests session objects for more flexibility.

  print(type(tr.ctrl))            # a stem.control.Controller object
  tr.ctrl.signal('CLEARDNSCACHE') # see Stem docs for the full API

  print(type(tr.session))         # a requests.Session object
```

## License
MIT


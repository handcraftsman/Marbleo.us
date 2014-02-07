# Marbleo.us

Marbleo.us is an interactive marble run creator written in CoffeeScript.

It took part in the InformatiCup 2011 &mdash; unfortunately, it did not win any awards.

- Try the [app][app], live in your Browser.
- Browse the [Wiki][wiki] for more information (German only).
- Read our [source code][source]

environment:

- install [node.js][node.js]
- npm install -g coffee-script
- npm install node-fs-extra
- install [ruby][ruby]
- windows only: add c:\[ruby install dir]\bin to your path
- gem install sass
- gem install haml

build:

- cake build
- cake test
- cake minify (if you have the closure compiler installed)

[app]:    http://marbleo.us
[wiki]:   https://github.com/robb/Marbleo.us/wiki
[source]: http://marbleo.us/docs/Main.html
[node.js]: http://nodejs.org/
[ruby]: http://rubyinstaller.org/downloads/
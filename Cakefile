sys    = require 'sys'
fs     = require 'fs'
{exec} = require 'child_process'
fs2	   = require('node-fs-extra')

buildFiles = [
  "Constants",
  "EventEmitter",
  "Block",
  "Marble",
  "Path",
  "Map",
  "TextureStore",
  "Layer",
  "HitTestLayer",
  "MapLayer",
  "VisibilityLayer",
  "Renderer",
  "Animator",
  "Compressor",
  "Game",
  "Palette",
  "Main"
]

task 'build', 'Build application from source files', ->
  sys.puts 'Compiling HAML files'
  fs.exists 'bin', (itExists) -> 
    if not itExists then fs2.mkdirp 'bin'
    fs.readdir 'src/', (err, files) ->
      for file in files
        continue unless file.match /haml$/
        newFile = file.replace /haml/g, 'html'
        sys.puts "compiling src/#{file}"
        exec "haml -f html5 src/#{file} bin/#{newFile}", (err) ->
          throw err if err

  sys.puts 'Compiling SASS files'
  fs.exists 'bin/css', (itExists) -> 
    if not itExists then fs2.mkdirp 'bin/css'
    fs.exists 'src/css', (itExists) -> 
      if not itExists then fs2.mkdirp 'src/css'
      fs.readdir 'src/css', (err, files) ->
        for file in files
          continue unless file.match /s(c|a)ss$/
          newFile = file.replace /s(c|a)ss/g, 'css'
          sys.puts "compiling src/css/#{file}"
          exec "sass src/css/#{file} bin/css/#{newFile}", (err) ->
            throw err if err

  sys.puts 'Copying images'
  fs.exists 'bin/img', (itExists) -> 
    if not itExists then fs2.mkdirp 'bin/img'
    fs2.copy 'src/img', 'bin/img', (err) ->
      throw err if err
# fs.createReadStream('test.log').pipe(fs.createWriteStream('newLog.log'));
  sys.puts 'Compiling CoffeeScript files'
  fs.exists 'bin/js', (itExists) -> 
    if not itExists then fs2.mkdirp 'bin/js'
    appContents = new Array remaining = buildFiles.length
    counter = 0
    for file in buildFiles
      fs.readFile "src/js/#{file}.coffee", 'utf8', (err, fileContents) ->
        throw err if err
        appContents[counter++] = fileContents
        process() unless --remaining
    process = ->
      sys.puts 'Writing bin/js/marbleous.coffee'
      fs.writeFile 'bin/js/marbleous.coffee', appContents.join('\n\n'), 'utf8', (err) ->
        throw err if err
        exec 'coffee --compile bin/js/marbleous.coffee', (err, stdout, stderr) ->
          throw err if err
          sys.print stdout + stderr
          fs.unlink 'bin/js/marbleous.coffee', (err) ->
            throw err if err
            sys.puts 'Done.'

# TODO: Make this work on Simon's machine
# TODO: Try to resolve marbleo.us.test before opening the browser,
#       to make sure the testing server is set up properly
task 'test', 'Compile the app for testing, try opening a browser', ->
  sys.puts 'Compiling CoffeeScript files for test'
  fs.exists 'test/js', (itExists) -> 
    if not itExists then fs2.mkdirp 'test/js'

  appContents = new Array remaining = buildFiles.length
  counter = 0
  for file in buildFiles
    fs.readFile "src/js/#{file}.coffee", 'utf8', (err, fileContents) ->
      throw err if err
      appContents[counter++] = fileContents
      process() unless --remaining
  process = ->
    fs.writeFile 'test/js/marbleous.coffee', appContents.join('\n\n'), 'utf8', (err) ->
      throw err if err
      exec 'coffee --bare --compile test/js/marbleous.coffee', (err, stdout, stderr) ->
        throw err if err
        sys.print stdout + stderr
        fs.unlink 'test/js/marbleous.coffee', (err) ->
          throw err if err
          sys.puts 'Done.'

  sys.puts 'Opening browser...'
  exec 'open http://marbleo.us.test'

task 'minify', 'Minify the resulting application file after build', ->
  sys.puts 'Compiling using ADVANCED_OPTIMIZATIONS'
  exec "closure --compilation_level ADVANCED_OPTIMIZATIONS
                --summary_detail_level 3
                --warning_level VERBOSE
                --js bin/js/marbleous.js
                --js_output_file bin/js/marbleous-min.js
                --warning_level QUIET
                --externs lib/jquery-1.4.4.js
                --externs lib/webkit_console.js", (err, stdout, stderr) ->
    sys.print stdout + stderr
    copy()

  copy = ->
    sys.puts "Overwriting old version with result"
    fs.exists 'test/js', (itExists) -> 
	  if itExists then fs.rename 'bin/js/marbleous-min.js', 'bin/js/marbleous.js', (err) ->
        throw err if err

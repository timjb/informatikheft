child_process = require 'child_process'
crypto        = require 'crypto'
path          = require 'path'
fs            = require 'fs'
http          = require 'http'

# These modules have to be installed with npm
jsdom         = require 'jsdom'      # tmpvar/jsdom
async         = require 'async'      # coalan/async
bufferlist    = require 'bufferlist' # substack/node-bufferlist
mustache      = require 'mustache'   # janl/mustache


fileCache = (fn, type, getFileName) ->
  return (args..., callback) ->
    md5 = crypto.createHash('md5').update(args.toString()).digest('hex')
    file_name = getFileName md5
    
    path.exists file_name, (does_it) ->
      if does_it
        if type == 'path'
          callback file_name
        else
          fs.readFile file_name, 'utf8', (err, contents) ->
            throw err if err
            callback contents
      else
        fn args..., (result) ->
          # result should be a string or buffer
          fs.writeFile file_name, result, 'utf8', (err) -> throw err if err
          callback (if type == 'path' then file_name else result)


# Alternative implementation
#def yuml_img(yuml_code, options)
#  md5 = Digest::MD5.new.hexdigest yuml_code
#  sh "./scruffy/yuml2dot.py --png \"#{yuml_code}\" > #{md5}.png"
#end

loadYumlImage = (code, options, callback) ->
  code = code.replace /\n/g, ','
  code = encodeURIComponent(code).replace '+', '%20'
  options = ';' + options if options
  path = "/diagram/plain#{options}/class/#{code}"
  
  buffers = new bufferlist.BufferList()
  http.get { host: 'yuml.me', path: path }, (response) ->
    response.on 'data', (data) -> buffers.push data
    response.on 'end', -> callback buffers.join()
  console.log "Loading yuml image #{code.slice 0, 50}..."

loadYumlImage = fileCache loadYumlImage, 'path', (md5) -> "assets/yuml/#{md5}.png"


# install pygments:
# - sudo apt-get install python-pip
# - sudo easy_install Pygments

pygmentize = (code, language, callback) ->
  pygmentize_process = child_process.exec "pygmentize -l #{language} -f html", (err, stdout) ->
    throw err if err
    callback stdout
  pygmentize_process.stdin.end code, 'utf8'
  console.log "Pygmentizing #{code.slice 0, 50}..."

pygmentize = fileCache pygmentize, 'contents', (md5) -> "tmp/pygmentize/#{md5}"


replaceElement = (old, neww) ->
  old.parentNode.replaceChild neww, old


processPre = (pre, callback) ->
  content = pre.firstChild.innerHTML
  lines   = content.split '\n'
  header  = lines[0]
  code    = lines.slice(1).join('\n')
  if header.match /^#highlight/
    language = header.replace(/^#highlight\s*/, '').trim()
    pygmentize code, language, (highlighted_html) ->
      document = pre.ownerDocument
      div = document.createElement 'div'
      div.innerHTML = highlighted_html
      replaceElement pre, div
      callback()
  else if header.match /^#websequencediagram/
    pre.innerHTML = code
    pre.className = 'websequencediagram'
    process.nextTick callback
  else if header.match /^#yuml/
    options = header.replace(/^#yuml\s*/, '').trim()
    for option in options.split /\s+/
      [key, value] = option.split ':'
      pre.setAttribute 'data-'+key, value
    loadYumlImage code, options, (file_name) ->
      document = pre.ownerDocument
      img = document.createElement 'img'
      img.className = 'diagram'
      img.alt = "Class diagram"
      img.src = file_name
      replaceElement pre, img
      callback()
  else
    # do nothing
    process.nextTick callback


applyTemplate = (view, callback) ->
  fs.readFile 'template.html', 'utf8', (err, template) ->
    throw err if err
    callback null, mustache.to_html(template, view)


task 'compile', ->
  async.waterfall [
    (cb) -> child_process.exec "pandoc index.markdown", cb
    (html, _, cb) -> jsdom.env html, cb
    (window, cb) ->
      document = window._document
      title = document.getElementsByTagName('h1').innerText
      pres = document.getElementsByTagName 'pre'
      async.forEach pres, processPre, ->
        cb null, { title: title, html: document.body.innerHTML }
    (view, cb) -> applyTemplate view, cb
    (html, cb) -> fs.writeFile 'index.html', html, 'utf8', cb
    (cb) -> console.log 'Wrote index.html.'; process.nextTick cb
  ]


task 'watch', ->
  fs.watchFile 'index.markdown', ->
    console.log 'Change in index.markdown. Recompiling.'
    invoke 'compile'

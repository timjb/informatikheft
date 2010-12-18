require 'digest/md5'
require 'net/http'
require 'net/ftp'

require 'rubygems'
require 'kramdown'
require 'mustache'
require 'nokogiri'

#require 'curb'

#def yuml_img(yuml_code, options)
#  #yuml_code.gsub!("\t", "")
#  #yuml_code.gsub!("\n", "")
#  yuml_code = CGI.escape(yuml_code).gsub('+', '%20')
#  if not options.empty?
#    options = ';' + options
#  end
#  md5 = Digest::MD5.new.hexdigest yuml_code
#  
#  # Grab the ID, and then download img and save to disk
#  file_name = "media/#{md5}.png"
#  if not FileTest.exists?(file_name)
#    Net::HTTP.start("yuml.me") do |http|
#      print "Loading yuml.me diagram: #{yuml_code}\n"
#      resp = http.get("/diagram/plain#{options}/class/#{yuml_code}")
#      open(file_name, "wb") do |file|
#        file.write(resp.body)
#       end
#    end
#  end
#  file_name
#end

# install pygments:
# - sudo apt-get install python-pip
# - sudo easy_install Pygments
def pygmentize(code, language, params={})
  IO.popen("pygmentize -l #{language} -f html", "r+") do |io|
    io.write(code)
    io.close_write
    return io.read
  end
end

task :default do
  # Convert to HTML
  markdown = File.open('index.markdown').read
  html = Kramdown::Document.new(markdown, :parse_block_html => true).to_html
  
  # Parse the HTML Doc
  html_doc = Nokogiri::HTML::DocumentFragment.parse(html)
  # Get the title
  title = html_doc.at_css('h1').content
  # Get the yUML images
  html_doc.css('pre').each do |pre|
    content = pre.content
    lines = content.split("\n")
    if lines[0].match /^#yuml/i
      lines[0].sub!(/^#yuml\s*/i, '')
      lines.shift.strip.split(/\s+/).each do |option|
        key, value = option.split(':')
        pre.set_attribute('data-'+key, value)
      end
      pre.content = lines.join("\n")
      pre.set_attribute('class', 'yuml')
      #file_name = yuml_img(lines.join("\n"), options)
      #pre.replace "<img class=\"diagram\" alt=\"Klassendiagramm\" src=\"#{file_name}\" />"
    elsif lines[0].match /^#websequencediagram/i
      lines.shift
      pre.content = lines.join("\n")
      pre.set_attribute('class', 'websequencediagram')
    elsif lines[0].match /^#highlight/i
      lines[0].sub!(/^#highlight\s*/i, '')
      language = lines.shift
      lines.map! do |line|
        line.gsub "\t", ' '*4
      end
      pre.replace(pygmentize(lines.join("\n"), language))
    end
  end
  
  # Build webpage from template
  template = File.open('template.html').read
  site = Mustache.render template, :html => html_doc.to_html, :title => title
  File.new('index.html', 'w').write site
end

task :upload do
  Net::FTP.open('timbaumann.info') do |ftp|
    ftp.login 'w00b12dc', 'servereiderjoe'
    ftp.puttextfile 'index.html', 'subdomains/informatik/index.html'
  end
end

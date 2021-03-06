# mac users should have all in place
# 1. $gem install rake
# 2. $cd some/path/firerainbow
# 3. $rake
#
# win users:
# 1. install binary ruby (unzip to C:\ruby)
# 2. set C:\ruby\bin to system path
# 3. download and install ruby gems (http://www.rubygems.org/read/chapter/3)
# 5. solve dll problems:
# 5.a) download openssl 0.98e or later here (http://smithii.com/openssl), copy ssleay32.dll and libeay32.dll to your C:\ruby\bin
# 5.b) download zlib binary here (http://gnuwin32.sourceforge.net/downlinks/zlib-bin-zip.php), rename zlib1.dll to zlib.dll, and copy it to your C:\ruby\bin
# 6. >gem install win32console
# 7. >gem install rake
# 8. install zip to system path (http://www.info-zip.org/Zip.html)
# 9. >cd some\path\firerainbow
#10. >rake

ROOT = File.expand_path('.')
SRC = ROOT
DST = File.join(ROOT, 'build')
TMP = File.join(ROOT, 'tmp')

# http://kpumuk.info/ruby-on-rails/colorizing-console-ruby-script-output/
begin
  require 'Win32/Console/ANSI' if PLATFORM =~ /win32/
rescue LoadError
  raise 'You must "gem install win32console" to use terminal colors on Windows'
end

def colorize(text, color_code)
  "#{color_code}#{text}\e[0m"
end

def red(text); colorize(text, "\e[31m"); end
def green(text); colorize(text, "\e[32m"); end
def yellow(text); colorize(text, "\e[33m"); end
def blue(text); colorize(text, "\e[34m"); end
def magenta(text); colorize(text, "\e[35m"); end
def azure(text); colorize(text, "\e[36m"); end
def white(text); colorize(text, "\e[37m"); end
def black(text); colorize(text, "\e[30m"); end

def file_color(text); yellow(text); end
def dir_color(text); blue(text); end
def cmd_color(text); azure(text); end

#
# you can use FileUtils: http://corelib.rubyonrails.org/classes/FileUtils.html
#
require 'find'

# copies directory tree without .svn, .git and other temporary files
def cp_dir(src, dst)
  puts "#{cmd_color('copying')} #{dir_color(src)}"
  puts "     -> #{dir_color(dst)}"
  Find.find(src) do |fn|
    next if fn =~ /\/\./
    r = fn[src.size..-1]
    if File.directory? fn
      mkdir File.join(dst,r) unless File.exist? File.join(dst,r)
    else
      cp(fn, File.join(dst,r))
    end
  end
end

def cp_file(src, dst)
  puts "#{cmd_color('copying')} #{file_color(src)}"
  puts "     -> #{file_color(dst)}"
  cp(src, dst)
end

def dep(src)
  s = File.expand_path src
  rs = s[SRC.size..-1]
  d = File.join(TMP, rs)
  puts "#{cmd_color('copying')} #{file_color(s)}"
  puts "     -> #{file_color(d)}"
  cp(s, d)
end

def my_mkdir(dir)
  puts "#{cmd_color('creating directory')} #{dir_color(dir)}"
  mkdir dir
end

def parse_version()
  f = File.new(File.join(SRC, 'install.rdf'))
  text = f.read
  unless text=~/<em:version>([^<]*)<\/em:version>/
    puts "#{red('Version not found')}"
    exit
  end
  $1
end

################################################################################

task :default do
  version = parse_version()

  remove_dir(TMP) if File.exists?(TMP) # recursive!
  mkdir(TMP)
  cp_dir(File.join(SRC, 'chrome'), File.join(TMP, "chrome"))
  cp_dir(File.join(SRC, 'defaults'), File.join(TMP, "defaults"))
  dep(File.join(SRC, 'chrome.manifest'))
  dep(File.join(SRC, 'install.rdf'))
  dep(File.join(SRC, 'license.txt'))
  my_mkdir(DST) unless File.exist?(DST)

  res = "#{DST}/firerainbow-#{version}.xpi"
  File.unlink(res) if File.exists?(res)
  puts "#{cmd_color('zipping')} #{file_color(res)}"
  Dir.chdir(TMP) do
    puts red('need zip on command line (download http://www.info-zip.org/Zip.html)') unless system("zip -r \"#{res}\" *");
  end
  remove_dir(TMP) if File.exist?(TMP) # recursive!
end
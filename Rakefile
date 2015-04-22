require 'fileutils'

AROUND_ROOT        = ENV["AROUND_ROOT"] || File.join(File.dirname(File.expand_path(__FILE__)))
AROUND_MRUBY_ROOT  = File.join(AROUND_ROOT, "mruby")
AROUND_GEMBOX_ROOT = File.join(AROUND_ROOT, "mrbgems")
AROUND_MAIN_ROOT  = File.join(AROUND_ROOT, "lib", "main")

if ENV["MRUBY_CONFIG"] == File.expand_path(__FILE__)
  MRuby::Build.new do |conf|
    # load specific toolchain settings

    # Gets set by the VS command prompts.
    if ENV['VisualStudioVersion']
      toolchain :visualcpp
    else
      toolchain :gcc
    end

    conf.gembox File.join(AROUND_ROOT, "mrbgems", "around")
  end
else
  task :default => :build

  desc "Execute Sample"
  task :execute => :build do
    FileUtils.cd File.join(AROUND_ROOT, "out")
    exit sh("./sample")
  end

  desc "Setup env"
  task :env do
    FileUtils.cd AROUND_MRUBY_ROOT

    if ENV["CONFIG"]
      ENV["MRUBY_CONFIG"] = File.join(File.expand_path(File.dirname(__FILE__)), ENV["CONFIG"])
    else
      ENV["MRUBY_CONFIG"] = File.expand_path(__FILE__)
    end
  end

  desc "Compile MRuby"
  task :mruby => :env do
    sh("rake")
  end

  desc "Compile src/main.c linking libmruby.a"
  task :build => :mruby do
    FileUtils.cd AROUND_MAIN_ROOT
    sh("bundle install")
    sh("rake")
    FileUtils.cd AROUND_ROOT
    FileUtils.rm_rf "out"
    FileUtils.mkdir_p "out"
    FileUtils.cp_r File.join(AROUND_MAIN_ROOT, "out", "main"), "out/"
    sh("gcc -o out/sample -I mruby/include/ -m32 mruby/build/32bit/lib/libmruby.a src/main.c")
  end

  desc "Clean"
  task :clean => :env do
    FileUtils.rm_rf File.join(AROUND_ROOT, "out")
    exit sh("rake clean")
  end

  desc "Setup Project and Submodules"
  task :setup do
    sh "git submodule init"
    sh "git submodule update"
    FileUtils.cd File.join(AROUND_GEMBOX_ROOT, "mruby-qrcode")
    sh "git submodule init"
    exit sh "git submodule update"
  end
end

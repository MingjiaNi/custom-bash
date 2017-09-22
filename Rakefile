require 'rake'
require 'erb'

desc "install the dot files into user's home directory"
task :install do
  replace_all = false
  Dir['*'].each do |file|
    next if %w[Rakefile README.rdoc LICENSE].include? file

    if File.exist?(File.join(ENV['HOME'], ".#{file.sub('.erb', '')}"))
      if File.identical? file, File.join(ENV['HOME'], ".#{file.sub('.erb', '')}")
        puts "identical ~/.#{file.sub('.erb', '')}"
      elsif replace_all
        replace_file(file)
      else
        print "overwrite ~/.#{file.sub('.erb', '')}? [ynaq] "
        case $stdin.gets.chomp
        when 'a'
          replace_all = true
          replace_file(file)
        when 'y'
          replace_file(file)
        when 'q'
          exit
        else
          puts "skipping ~/.#{file.sub('.erb', '')}"
        end
      end
    else
      link_file(file)
    end
  end
end

task :restore_backup do
    Dir["#{backup_path}/*"].each do |file|
        if File.exist?(File.join(ENV['HOME'], ".#{file.sub('.erb', '')}"))
            if !(File.identical? file, File.join(ENV['HOME'], ".#{file.sub('.erb', '')}"))
                restore_file(file)
            end
        end
    end
end

def backup_path
  "$HOME/Documents/.bash_backup"
end

def replace_file(file)
  backup_file(file)
  link_file(file)
end

def backup_file(file)
    system %Q{rm -rf "#{backup_path}"}
    system %Q{mkdir "#{backup_path}"}
    system %Q{mv "$HOME/.#{file.sub('.erb', '')}" "#{backup_path}/.#{file.sub('.erb', '')}"}
    print "File #{file.sub('.erb', '')} has been stored to #{backup_path}"
end

def restore_file(file)
    system %Q{mv "#{backup_path}/#{file}" "$HOME/.#{file}"}
end

def link_file(file)
  if file =~ /.erb$/
    puts "generating ~/.#{file.sub('.erb', '')}"
    File.open(File.join(ENV['HOME'], ".#{file.sub('.erb', '')}"), 'w') do |new_file|
      new_file.write ERB.new(File.read(file)).result(binding)
    end
  else
    puts "linking ~/.#{file}"
    system %Q{ln -s "$PWD/#{file}" "$HOME/.#{file}"}
  end
end

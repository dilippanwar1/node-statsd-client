require "fileutils"

NODE_STATSD_CLIENT_VERSION = '0.0.6'
RELEASE_PREFIX = '1soundcloud'
RELEASE = RELEASE_PREFIX + (ENV['BUILD_NUMBER'] || 'XXXXX')

task :default do
  puts "Please run rake -T to get a listing of available tasks."
end

desc "Produce a debian package of node-statsd-client."
task :package => [:tmpdir, :export_dir, :git_export] do
  # Ensure the destination package file is not already present - fpm freaks out
  output_filename = "node-statsd-client_#{NODE_STATSD_CLIENT_VERSION}-#{RELEASE}_amd64.deb"
  File.unlink(output_filename) if File.exist?(output_filename)

  # Collect arguments to fpm
  fpm_args = %W{
    -t deb
    -s dir
    -n node-statsd-client
    -v #{NODE_STATSD_CLIENT_VERSION}
    --iteration #{RELEASE}
    -C #{@tmpdir}
    --deb-user root
    --deb-group root
    srv
  }.join(' ')

  # Shell out to fpm as it doesn't play nicely as a library
  sh "fpm #{fpm_args}"
  Rake::Task[:cleanup].invoke
end

# Sets up the temporary directory for the build
task :tmpdir do
  require 'tempfile'
  @tmpdir = Dir.mktmpdir()
end

# Creates the target directory for the "git archive" export of the repository
task :export_dir => :tmpdir do
  @export_dir = File.join(@tmpdir, "srv/statsd")
  FileUtils.mkdir_p(@export_dir)
end

# Exports a clean copy of the git repository
task :git_export => :export_dir do
  release_tag = "v#{NODE_STATSD_CLIENT_VERSION}"
  sh "git archive #{release_tag} | tar -x -C #{@export_dir}"
end

# Cleans up the temporary directory containing the build
task :cleanup => :tmpdir do
  FileUtils.rm_rf(@tmpdir, :verbose => true)
end

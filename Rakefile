#
# For the `release` task
#
require 'bundler/gem_tasks'

#
# For automatic creation of github releases
#
require 'chandler/tasks'

#
# Add chandler as a prerequisite for `rake release`
#
task 'release:rubygem_push' => 'chandler:push'

#
# Prepend DevKit into compilation phase
#
if Gem.win_platform?
  desc 'Activates DevKit'
  task :devkit do
    begin
      require 'devkit'
    rescue LoadError
      abort 'Failed to load DevKit required for compilation'
    end
  end

  task compile: :devkit
end

#
# For the `compile` task
#
require 'rake/extensiontask'

spec = Gem::Specification.load('byebug.gemspec')
Rake::ExtensionTask.new('byebug', spec) { |ext| ext.lib_dir = 'lib/byebug' }

#
# Test task
#
desc 'Runs the test suite'
task :test do
  require_relative 'script/minitest_runner'

  exit 1 unless MinitestRunner.new.run
end

desc 'Run overcommit hooks manually'
task :overcommit do
  exit 1 unless system('bundle exec overcommit --run')
end

desc 'Sign overcommit hooks'
task :sign_hooks do
  system('bundle exec overcommit --sign')
  system('bundle exec overcommit --sign pre-commit')
end

desc 'Build docker images'
task :build_docker_images do
  require_relative 'docker/manager'

  Docker::Manager.build_all
end

desc 'Push docker images to dockerhub'
task :push_docker_images do
  require_relative 'docker/manager'

  Docker::Manager.push_all
end

task default: %i[compile test overcommit]

#
# Custom tasks for development
#
require_relative 'tasks/dev_utils.rb'

#
# Generate docs
#
require 'yard'
YARD::Rake::YardocTask.new

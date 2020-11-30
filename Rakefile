# coding: utf-8

require 'yaml'

task default: :clean

task :clean do
  puts "Clean"
  puts "Remove all deployment folders"
  sh "rm -rf _deploy"
end

desc "Deploy to live branch <environment> <version> <comment>"
task deploy: :clean do
  ARGV.each { |a| task a.to_sym do ; end }
  puts "Deploy"

  environment = ARGV[1] || 'staging'
  version = ARGV[2]
  comment = ARGV[3]
  is_production = environment == 'production'

  puts "{\n
        Environment: #{environment},
        Version: #{version},
        Comment: #{comment},
        \n}"

  config_file = is_production ? "_config.yml" : "_config_staging.yml"

  if is_production
    errors = []
    errors << "Version tag is required" unless version
    errors << "A simple comment is required" unless comment

    unless errors.empty?
      errors.each { |e| puts e }
      exit 1
    end
  end

  config = YAML.load(File.new(config_file))["deploy"]
  deploy_dir = "_deploy/#{config["dir"]}"
  deploy_branch = config["branch"]
  deploy_source = config["source"]
  origin = `pwd`

  sh "git clone #{deploy_source} \"#{deploy_dir}\""
  sh "cd #{deploy_dir} ;
      git checkout -b \"#{deploy_branch}\" ;
      git push origin \"#{deploy_branch}\" ;
      #{is_production ? "git tag -a \"#{version}\" -m \"#{comment}\" ; git push --tags" : ""}"
end

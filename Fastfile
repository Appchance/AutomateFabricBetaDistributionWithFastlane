# This file contains the fastlane.tools configuration
# You can find the documentation at https://docs.fastlane.tools
#
# For a list of all available actions, check out
#
#     https://docs.fastlane.tools/actions
#
# For a list of all available plugins, check out
#
#     https://docs.fastlane.tools/plugins/available-plugins
#

# Uncomment the line if you want fastlane to automatically update itself
# update_fastlane

default_platform(:ios)

#Globals
prefix_dir = "./"
project_name = "YOUR_PROJECT_NAME"
project_dir = "#{prefix_dir}#{project_name}/"
project_scheme = "#{project_name}"  
project_output_name = "#{project_scheme}_AdHoc"
xcodeproj = "#{prefix_dir}#{project_name}.xcodeproj"
workspace = "#{prefix_dir}#{project_name}.xcworkspace"
build_dir = "./build"
 
#Crashlytics
crashlytics_api_token = "YOUR_CRASHLYTICS_API_TOKEN"
crashlytics_build_secret = "YOUR_CRASHLYTICS_BUILD_SECRET"
crashlytics_emails = [EMAILS_SEPARATED_BY_COMMAS]

platform :ios do
    lane :increment_build_and_version_numbers do 
        increment_build_number
        increment_version_number
    end
  
    lane :archive_adhoc do
        Dir.mkdir("../#{build_dir}") unless File.exists?("../#{build_dir}")
       
        build_app(
          scheme: project_scheme,
          output_name: "#{project_output_name}",
          workspace: workspace,
          configuration: "Release",
          export_method: "ad-hoc",
          output_directory: "#{build_dir}"
        )
      end
  
    lane :upload_to_crashlytics do
        crashlytics(
          api_token: crashlytics_api_token,
          build_secret: crashlytics_build_secret,
          emails: crashlytics_emails
        )  
          upload_symbols_to_crashlytics(dsym_path: "#{build_dir}/#{project_output_name}.app.dSYM.zip")
    end
  
    lane :commit_beta_version do
        version = get_version_number(
          target: project_scheme,
          xcodeproj: xcodeproj,
        )
        sh "git commit -am 'Fabric Beta version: #{version}'"
        sh "git tag -a v#{version} -m 'Fabric Beta'"
        sh "git push"
    end
  
    lane :fabric_beta do
        increment_build_and_version_numbers
        archive_adhoc
        upload_to_crashlytics
        commit_beta_version
    end
end

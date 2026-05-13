opt_out_usage

default_platform(:ios)

# ──────────────────────────────────────────────
# ANDROID
# ──────────────────────────────────────────────
platform :android do

  desc "Upload AAB to Google Play"
  lane :deploy do |options|
    track    = options[:track] || "internal"
    aab_path = options[:aab]   || "../build/app/outputs/bundle/release/app-release.aab"

    upload_to_play_store(
      track:                    track,
      aab:                      aab_path,
      json_key_data:            ENV["GOOGLE_PLAY_JSON_KEY"],
      skip_upload_apk:          true,
      skip_upload_metadata:     true,
      skip_upload_changelogs:   true,
      skip_upload_images:       true,
      skip_upload_screenshots:  true,
    )

    UI.success("✅ Android uploaded to #{track}")
  end

end

# ──────────────────────────────────────────────
# iOS
# ──────────────────────────────────────────────
platform :ios do

  desc "Build and upload IPA to TestFlight or App Store"
  lane :deploy do |options|
    track      = options[:track]      || "testflight"
    bundle_id  = options[:bundle_id]  || ENV["BUNDLE_ID"]

    api_key = app_store_connect_api_key(
      key_id:        ENV["ASC_KEY_ID"],
      issuer_id:     ENV["ASC_ISSUER_ID"],
      key_content:   ENV["ASC_PRIVATE_KEY"],
      is_key_content_base64: false,
      in_house: false,
    )

    match(
      type:           "appstore",
      readonly:       true,
      app_identifier: bundle_id,
      api_key:        api_key,
    )

    build_app(
      workspace:        "ios/Runner.xcworkspace",
      scheme:           "Runner",
      configuration:    "Release",
      export_method:    "app-store",
      output_directory: "build/ios",
      output_name:      "Runner.ipa",
    )

    if track == "production"
      deliver(
        ipa:               "build/ios/Runner.ipa",
        api_key:           api_key,
        submit_for_review: false,
        skip_metadata:     true,
        skip_screenshots:  true,
        force:             true,
      )
    else
      upload_to_testflight(
        ipa:                               "build/ios/Runner.ipa",
        api_key:                           api_key,
        skip_waiting_for_build_processing: true,
      )
    end

    UI.success("✅ iOS uploaded to #{track}")
  end

end

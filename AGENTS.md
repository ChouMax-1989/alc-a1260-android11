# A1260 Android 11 releases

- Maintain the complete public firmware bundle and plain Chinese installation/update documentation for ALC/AIC A1260 (MT6580, ARM32 Binder32).
- Use only the verified device partition layout. Keep exact image sizes and SHA-256 in the release metadata; do not substitute a generic MT6580 image or enable Format All.
- Publish only sanitized, generic materials. Never include accounts, credentials, Wi-Fi settings, Bluetooth pairing, serials, private recordings, learned IR codes, NVRAM/protect/userdata images or raw research backups.
- Distinguish first installation (which may apply an explicitly documented clean app profile) from update of an existing device (which preserves existing app data and identity).
- Verify the new release archive and public download before retiring older releases. Preserve local original backups and rollback artifacts.
- Document actual tested boundaries. App/configuration fixes packaged alongside unchanged verified system/boot images are not a newly compiled kernel or a new full-flash acceptance test.
- Do not bundle, install, update, uninstall or reconfigure XiaoZhi AI as part of this firmware or its setup/update scripts. XiaoZhi is maintained as a separate app and repository.

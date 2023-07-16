1. Executar

```shell
     keytool -genkey -v -keystore ~/upload-keystore.jks -keyalg RSA \
          -keysize 2048 -validity 10000 -alias upload
   ```
   
2. Criar arquivo em [project]/android/key.properties

```properties
   storePassword=<password-from-previous-step>
   keyPassword=<password-from-previous-step>
   keyAlias=upload
   storeFile=<keystore-file-location>
```

3. Adicionar em [project]/android/app/build.gradle

```groovy
   def keystoreProperties = new Properties()
   def keystorePropertiesFile = rootProject.file('key.properties')
   if (keystorePropertiesFile.exists()) {
       keystoreProperties.load(new FileInputStream(keystorePropertiesFile))
   }

   android {
       "..."
   }
```

4.  Substituir código em [project]/android/app/build.gradle

### Antes
```groovy
   buildTypes {
       release {
           // TODO: Add your own signing config for the release build.
           // Signing with the debug keys for now,
           // so `flutter run --release` works.
           signingConfig signingConfigs.debug
       }
   }

```

### Depois

```groovy
   signingConfigs {
       release {
           keyAlias keystoreProperties['keyAlias']
           keyPassword keystoreProperties['keyPassword']
           storeFile keystoreProperties['storeFile'] ? file(keystoreProperties['storeFile']) : null
           storePassword keystoreProperties['storePassword']
       }
   }
   buildTypes {
       release {
           signingConfig signingConfigs.release
       }
   }

```

### Referências

- [Lab: Write your first Flutter app](https://docs.flutter.dev/get-started/codelab)
- [Cookbook: Useful Flutter samples](https://docs.flutter.dev/cookbook)
- [online documentation](https://docs.flutter.dev/)
- [signapp](https://docs.flutter.dev/deployment/android)
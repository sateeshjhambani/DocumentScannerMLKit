# DocumentScannerMLKit

This sample app outlines the surface-level usage of Google's ML Kit Document Scanner API. It can be used to digitize documents. The API also provides a built-in interface to transform scanned docs, docs can be cropped and rotated, and corners can be adjusted as well.

https://github.com/sateeshjhambani/DocumentScannerMLKit/assets/60574717/7cf191fd-f58d-4bf7-b025-791417658720

![d580c3d6-97d8-402e-b8d0-df8fcbd5ac60](https://github.com/sateeshjhambani/DocumentScannerMLKit/assets/60574717/24f1f5b1-3228-468b-8410-4c93eab9e66e)

## Usage

```kotlin
val options = GmsDocumentScannerOptions.Builder()
    .setScannerMode(SCANNER_MODE_FULL)
    .setGalleryImportAllowed(true)
    .setPageLimit(5)
    .setResultFormats(RESULT_FORMAT_JPEG, RESULT_FORMAT_PDF)
    .build()
val scanner = GmsDocumentScanning.getClient(options)
```
Registering a request to start the scanner activity for result. The onResult callback is called after the scan, we map scanned docs to URIs that we can show on the UI, alongside saving it in the app's internal storage.

```kotlin
val scannerLauncher = rememberLauncherForActivityResult(
    contract = ActivityResultContracts.StartIntentSenderForResult(),
    onResult = { activityResult ->
        if (activityResult.resultCode == RESULT_OK) {
            val result =
                GmsDocumentScanningResult.fromActivityResultIntent(
                    activityResult.data
                )
            imageUris = result?.pages?.map { it.imageUri } ?: emptyList()

            result?.pdf?.let { pdf ->
                val fileOutputStream =
                    FileOutputStream(File(filesDir, "scan.pdf"))
                contentResolver.openInputStream(pdf.uri)?.use {
                    it.copyTo(fileOutputStream)
                }
            }
        }
    }
)
```
Retrieving and launch the scanner activity intent

```kotlin
scanner.getStartScanIntent(this@MainActivity)
    .addOnSuccessListener {
        scannerLauncher.launch(
            IntentSenderRequest.Builder(it).build()
        )
    }
    .addOnFailureListener {
        Toast.makeText(
            applicationContext,
            it.message,
            Toast.LENGTH_LONG
        ).show()
    }
```

## References

[Docs](https://developers.google.com/ml-kit/vision/doc-scanner)

[Android Developes](https://android-developers.googleblog.com/2024/02/ml-kit-document-scanner-api.html)

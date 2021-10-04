# inImageHelperAdvance

Helps organisations to collect pictures of customerID cards &amp; Selfies in a more secure &amp; friendly manner. It is accompanied by sanity check methods, quality of picture assistance, liveness detection, face match between selfie and customerID cards &amp; official database verification of ID cards with ID images as well as with ID number.

## Minimum Requirements

- `minSdkVersion 18`
- `AndroidX`

## Getting Started

Add following lines in your root `build.gradle`

```
allprojects {
    repositories {
        ...
        maven { url "https://gitlab.com/api/v4/projects/24251481/packages/maven" }
    }
}
```

Add following lines in your module level `build.gradle`

```
dependencies {
    ....
    implementation 'co.invoid.android:imagehelperadvance:1.0.2'
}
```

This library also uses some common android libraries. So if you are not already using them then make sure you add these libraries to your module level `build.gradle`

- `androidx.appcompat:appcompat:1.2.0`
- `androidx.constraintlayout:constraintlayout:2.0.4`
- `com.google.android.material:material:1.3.0`

Since this library is using compiled native code so we recommend to split the apk based on the architecture of the processor to reduce your app size. However, if you are using
App Bundle to publish app then you don't have to do anything.
Use following code to split the apk.

```
android {
    splits {

        // Configures multiple APKs based on ABI.
        abi {

            // Enables building multiple APKs per ABI.
            enable true

            // Specifies that we do not want to also generate a universal APK that includes all ABIs.
            universalApk false
        }
    }
}
```

## Initialize SDK

```
yourinitbutton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                ImageHelperOptions imageHelperOptions = new ImageHelperOptions.Builder()
                  .setPhotoOption(ImageHelperOptions.PhotoOptions.SELFIE_WITH_DOC_PHOTO)
                  .setDocumentType(DocumentType.AADHAAR)
                  .setGalleryOption(ImageHelperOptions.GalleryOption.ALLOW_IN_DOC_ONLY)
                  .build();
              ImageHelper.with(this, "YOUR_AUTH_KEY", imageHelperOptions).start();
            }
        });
```

+ Use `ImageHelperOptions.Builder.enableAadhaarMasking()` to get masked aadhaar photo
+ Using `ImageHelperOptions.Builder.setPhotoOptions()` use can choose whether you want to take selfie, document images, database verification with ID number or any combination of these three. Use one of the following 
    - `ImageHelperOptions.PhotoOptions.SELFIE_ONLY`, 
    - `ImageHelperOptions.PhotoOptions.DOC_PHOTO_ONLY` or
    - `ImageHelperOptions.PhotoOptions.DOC_NUMBER_ONLY` or
    - `ImageHelperOptions.PhotoOptions.SELFIE_WITH_DOC_PHOTO` or
    - `ImageHelperOptions.PhotoOptions.SELFIE_WITH_DOC_NUMBER` or
    - `ImageHelperOptions.PhotoOptions.DOC_PHOTO_WITH_DOC_NUMBER` or
    - `ImageHelperOptions.PhotoOptions.SELFIE_WITH_DOC_PHOTO_AND_DOC_NUMBER`
+ We also support strict mode that prevents user from reaching onto next screen in case of any errors during api calls or if liveness detection enabled then user will not be able to goto next screen until proven live.
+ To enable strict mode on sdk use `ImageHelperOptions.Builder.enableStrictMode()`
+ To set Document type while using photo of document use `ImageHelperOptions.Builder.setDocumentType(DocumentType docType)`.
+ We support `DocumentType.AADHAAR`, `DocumentType.PAN`, `DocumentType.DRIVING_LICENSE`, `DocumentType.VOTER_ID` and `DocumentType.PASSPORT` for normal uploads and OCR.
+ To set Document type while using database verification using ID number use `ImageHelperOptions.Builder.setDocNumberType(DocumentType docType, Boolean param2)` where pass `true` as `param2` to enable strict mode on database verification using ID number.
+ We support `DocumentType.PAN`, `DocumentType.DRIVING_LICENSE`, `DocumentType.VOTER_ID` and `DocumentType.RC` for official database verification.
+ To enable or disable image from gallery option use `ImageHelperOptions.Builder.setGalleryOption()`. Use one of the following `ImageHelperOptions.GalleryOption.ALLOW_IN_DOC_ONLY`, `ImageHelperOptions.GalleryOption.ALLOW_IN_SELFIE_ONLY` to enable or disable in either of the screens. If you want to enable in all the screens then use `ImageHelperOptions.GalleryOption.ALLOW`, by default it is enabled in all screens.
+ To enable synchronous liveness detection in selfies use `ImageHelperOptions.Builder.enableLiveness(Boolean param1, Boolean param2)` where pass `true` as `param1` to enable asynchronous liveness and pass `true` as `param2` to enable strict mode on liveness. Default is false for both `param1` and `param2`.
+ To enable synchronous database verification in ID images use `ImageHelperOptions.Builder.enableDatabaseVerification(Boolean param1, Boolean param2)` where pass `true` as `param1` to enable asynchronous database verification and pass `true` as `param2` to enable strict mode on database verification. Default is false for both `param1` and `param2`.
+ To enable face match between selfie and ID card image make sure to use one of these photo options in `ImageHelperOptions.Builder.setPhotoOptions()`
    - `ImageHelperOptions.PhotoOptions.SELFIE_WITH_DOC_PHOTO` or 
    - `ImageHelperOptions.PhotoOptions.SELFIE_WITH_DOC_PHOTO_AND_DOC_NUMBER`, 
    - then to enable synchronous face match use `ImageHelperOptions.Builder.enableFaceMatch(Boolean param)` where pass `true` as `param` to enable asynchronous face match. Default value of `param` is `false`.

## Authorization

To Obtain your organisation's authkey, contact us at hello@invoid.co

## Response returned from the SDK

- Selfie file path `imageHelperResult.getSelfieResult().getImageFilePath()`
- Liveness result `imageHelperResult.getSelfieResult().getSelfieResult().getLivenessResult()`
- Document front image file path `imageHelperResult.getDocumentResult().getDocFrontPath()`
- Document back image file path `imageHelperResult.getDocumentResult().getDocBackPath()`
- Face similarity `imageHelperResult.getDocumentResult().getFaceSimilarity()`
- Glare in front document image `imageHelperResult.getDocumentResult().isGlareInFront()`
- Glare in back document image `imageHelperResult.getDocumentResult().isGlareInBack()`
- Blur in front document image `imageHelperResult.getDocumentResult().isBlurInFront()`
- Blur in back document image `imageHelperResult.getDocumentResult().isBlurInBack()`
- Face in document in front document image `imageHelperResult.getDocumentResult().isFaceInDoc()`
- Doc clarity in front document image `imageHelperResult.getDocumentResult().isDocFrontClear()`
- Doc clarity in back document image `imageHelperResult.getDocumentResult().isDocBackClear()`
- Database verification response using ID card image `imageHelperResult.getDocumentResult().getDbVerificationResponse()`
- Database verification response using ID card number `imageHelperResult.getDocNumberVerificationResult().getDocNumberResponse()`

```
@Override
    protected void onActivityResult(int requestCode, int resultCode, @Nullable Intent data) {
        if(requestCode == ImageHelper.PHOTO_HELPER_REQ_CODE) {

            if(resultCode == RESULT_OK) {
                ImageHelperResult imageHelperResult = data.getParcelableExtra(ImageHelper.IMAGE_RESULT);
                Log.d(TAG, "onActivityResult: "+imageHelperResult.toString());

           } else if(resultCode == ImageHelper.AUTHORIZATION_RESULT_CODE) {

           int authorizationResult = data.getIntExtra(ImageHelper.AUTHORIZATION_RESULT, -1);
                if(authorizationResult == ImageHelper.UNAUTHORIZED) {
                    Log.d(TAG, "onActivityResult: unauthorized");
                } else {
                    Log.d(TAG, "onActivityResult: authorization error");
                }
            }
        } else {
            super.onActivityResult(requestCode, resultCode, data);
        }
    }
```

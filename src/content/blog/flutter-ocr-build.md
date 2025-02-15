---
title: 'How to Implement OCR in Your Flutter App (Updated)'
pubDate: 2022-11-17T05:00:00Z
description: 'How to Implement OCR in Your Flutter App'
author: 'Codderlly'
image: '/blog/post-flutter-ocr-cover.jpg'
tags: ['flutter', 'dart']
---

## Introduction

Think of capturing a document, sign, or handwritten note with a set camera view and receiving the text immediately. That's the magic of *Optical Character Recognition (OCR)*. Integrating OCR into your Flutter app can significantly enhance user experience, enabling functionalities like digitizing printed text or automating data entry.
In this guide, we'll explore how to implement OCR in a Flutter application using up-to-date packages and best practices.

## Step 1: Selecting the Appropriate OCR Package
Flutter offers several packages to facilitate OCR capabilities. Here are some notable options:

* [**ocr_scan_text**](https://pub.dev/packages/ocr_scan_text): A wrapper around Google's ML Kit Text Recognition library providing the capability to accurately extract text from images or PDFs.
* [**flutter_tesseract_ocr**](https://pub.dev/packages/flutter_tesseract_ocr): Applies the Tesseract 4 engine, which is based on over 100 languages using network-based OCR engine with line-based recognition.
* [**flutter_vision_ocr**](https://pub.dev/packages/flutter_vision_ocr): Offers OCR capabilities based on the Vision API and allows text extraction from images as well as, real-time recognition from the camera of the device.

For this tutorial, we'll use **ocr_scan_text** due to its ease of integration and robust performance.
## Step 2: Adding Dependencies
Update your *pubspec.yaml* file to include the necessary dependencies (updated):

```yaml
dependencies:
    flutter: flutter:
        sdk: flutter
    ocr_scan_text: ^1.3.1
    image_picker: ^1.1.2
    permission_handler: ^11.3.1
```
    
* **ocr_scan_text**: Facilitates text recognition.
* **image_picker**: Enables image selection from the gallery or camera.
* **permission_handler**: Manages runtime permissions.
After updating, run:
```bash
flutter pub get
```
## Step 3: Configuring Permissions
Make sure that your application also has permissions for using the camera and storage:.
* **For Android**: Add the following to your *AndroidManifest.xml*:
```xml
<uses-permission android: name="android.permission.CAMERA" />
<uses-permission android: name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android: name="android.permission.WRITE_EXTERNAL_STORAGE" />

<application> <!-- Other configurations -->
    <activity android: name="com.google.mlkit.vision.text.TextRecognitionActivity" />
</application>
```
* **For iOS**: Update your *Info.plist*:
```xml
<key>NSCameraUsageDescription</key>
<string>We need access to your camera for text recognition.</string>
<key>NSPhotoLibraryUsageDescription</key>
<text>Access to your photo library is required for text recognition</text>.
```
## Step 4: Requesting Permissions at Runtime
Before requesting the camera or gallery access, get permission as follows:

```swift
import 'package:permission_handler/permission_handler.dart';

Future<void> requestPermissions() async {
  await [
    Permission.camera,
    Permission.storage,
  ].request();
}
```

Invoke *requestPermissions()* at an appropriate point in your app, such as during initialization or before accessing the camera.
## Step 5: Capturing or Selecting an Image
Grant users the ability to take a new photo, or choose from their gallery:
```dart
import 'package:image_picker/image_picker.dart';
import 'dart:io';

Future<File?> pickImage(ImageSource source) async {
  final picker = ImagePicker();
  final pickedFile = await picker.pickImage(source: source);

  return pickedFile != null ? File(pickedFile.path) : null;
}
```

Usage:
```dart
File? image = await pickImage(ImageSource.camera); // or ImageSource.gallery
```
## Step 6: Extracting Text from the Image
Utilize the *ocr_scan_text* package to process the image and extract text:
```dart
import 'package:ocr_scan_text/ocr_scan_text.dart';
import 'dart:io';

Future<String> extractText(File imageFile) async {
  final ocrText = await OcrScanText.recognize(imageFile.path);
  return ocrText;
}
```
## Step 7: Integrating into the UI
Combine the functionalities into a Flutter widget:
```dart
import 'package:flutter/material.dart';
import 'dart:io';

class OCRScreen extends StatefulWidget {
  @override
  _OCRScreenState createState() => _OCRScreenState();
}

class _OCRScreenState extends State<OCRScreen> {
  File? _image;
  String _extractedText = "";

  Future<void> _pickAndExtractText(ImageSource source) async {
    final image = await pickImage(source);
    if (image == null) return;

    setState(() {
      _image = image;
    });

    final text = await extractText(image);
    setState(() {
      _extractedText = text;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('OCR Demo')),
      body: SingleChildScrollView(
        child: Column(
          children: [
            if (_image != null) Image.file(_image!),
            ElevatedButton(
              onPressed: () => _pickAndExtractText(ImageSource.camera),
              child: Text("Capture Image"),
            ),
            ElevatedButton(
              onPressed: () => _pickAndExtractText(ImageSource.gallery),
              child: Text("Pick from Gallery"),
            ),
            Padding(
              padding: const EdgeInsets.all(16.0),
              child: Text(_extractedText),
            ),
          ],
        ),
      ),
    );
  }
}
```

## Step 8: Testing and Optimization
Test the OCR functionality with various text types:
* **Printed text**: Should yield high accuracy.
* **Handwritten text**: Accuracy may vary; consider using models specialized for handwriting.
* **Different languages**: Ensure the OCR engine supports the desired language.

For improved accuracy:

* Use high-quality images with clear text.
* Ensure proper lighting conditions during image capture.
* Preprocess images (e.g., adjusting contrast) before text extraction.

## Conclusion
Adding OCR capabilities to your Flutter application augments the app's ability to interact with the real world by converting text off of images to plain text. By following this guide, you can implement a robust OCR feature, providing users with a seamless and efficient text extraction experience.
Just take your time to try various OCR packages and parameters to see which are suitable to your application.
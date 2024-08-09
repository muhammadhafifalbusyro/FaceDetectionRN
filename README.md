# FaceDetectionRN

## Instalation

### 1.Instalation react-native-vision-camera (Lihat Dokumentasi)
```
import React, { useState, useRef,useEffect } from 'react';
import { Text, View, Button, Image } from 'react-native';
import { Camera, useCameraDevice } from 'react-native-vision-camera';
// import FaceDetection from '@react-native-ml-kit/face-detection';

const FaceScanner = ({ navigation, route }) => {
  const [cameraPermission] = useState(route.params.permission);
  const device = useCameraDevice('front');
  const camera = useRef(null);
  const [capturedPhoto, setCapturedPhoto] = useState(null);
  const [showPreview, setShowPreview] = useState(false);


 
  if (cameraPermission === null) {
    return <Text>Checking camera permission...</Text>;
  } else if (!cameraPermission) {
    return <Text>Camera permission not granted</Text>;
  }

  if (!device) {
    return <Text>No camera device available</Text>;
  }

  const takePhoto = async () => {
    try {
      if (!camera.current) {
        console.error('Camera reference not available.');
        return;
      }

      const photo = await camera.current.takePhoto();
      console.log(photo);
      
      if (photo) {
        setCapturedPhoto(`file://${photo.path}`);
        setShowPreview(true);
		// const faces = FaceDetection.detect(`file://${photo.path}`, { landmarkMode: 'all' });
		// console.error("faces",faces)
      } else {
        console.error('Photo captured is undefined or empty.');
      }
    } catch (error) {
      console.error('Error capturing photo:', error);
    }
  };

  const confirmPhoto = () => {
    console.log('Photo confirmed:', capturedPhoto);
    setShowPreview(false);
  };

  const retakePhoto = () => {
    setCapturedPhoto(null);
    setShowPreview(false);
  };

  return (
    <View style={{ flex: 1 }}>
      <Camera
        style={{ flex: 1 }}
        device={device}
        isActive={true}
        ref={camera}
        photo={true}
        video={true}
      />
      {showPreview && capturedPhoto ? (
        <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
          <Image
            source={{ uri: capturedPhoto }}
            style={{ width: 300, height: 300, marginBottom: 20 }}
          />
          <View style={{ flexDirection: 'row', justifyContent: 'space-between' }}>
            <Button title="Retake" onPress={retakePhoto} />
            <Button title="Confirm" onPress={confirmPhoto} />
          </View>
        </View>
      ) : (
        <View style={{ flexDirection: 'row', justifyContent: 'space-evenly' }}>
          <Button title="Take Photo" onPress={takePhoto} />
		  <Image source={{uri:'https://sumedang.radarbandung.id/wp-content/uploads/sites/2/2024/03/WhatsApp-Image-2024-03-17-at-19.30.39.jpeg'}} style={{height:50,width:50}}/>
        </View>
      )}
    </View>
  );
};

export default FaceScanner;

```

### 2. Instalation react-native-vision-camera-face-detector dan react-native-worklets-core (Lihat Dokumentasi)

```
import React, { useState, useRef,useEffect } from 'react';
import { Text, View, Button, Image } from 'react-native';
import { Camera, useCameraDevice, useFrameProcessor } from 'react-native-vision-camera';
// import FaceDetection from '@react-native-ml-kit/face-detection';
import { useFaceDetector } from 'react-native-vision-camera-face-detector'
import { Worklets } from 'react-native-worklets-core'

const FaceScanner = ({ navigation, route }) => {
  const [cameraPermission] = useState(route.params.permission);
  const device = useCameraDevice('front');
  const camera = useRef(null);
  const [capturedPhoto, setCapturedPhoto] = useState(null);
  const [showPreview, setShowPreview] = useState(false);

  const faceDetectionOptions = useRef({
    // detection options
  }).current

  const { detectFaces } = useFaceDetector(faceDetectionOptions)

//   const handleDetectedFaces = Worklets.createRunOnJS((faces) => { 
//     console.log('faces detected', faces)
//   })

  const frameProcessor = useFrameProcessor((frame) => {
    'worklet'
    const faces = detectFaces(frame)
    // ... chain frame processors
    // ... do something with frame
    // handleDetectedFaces(faces)
	console.log('faces detected', faces)
  }, [])


 
  if (cameraPermission === null) {
    return <Text>Checking camera permission...</Text>;
  } else if (!cameraPermission) {
    return <Text>Camera permission not granted</Text>;
  }

  if (!device) {
    return <Text>No camera device available</Text>;
  }

  const takePhoto = async () => {
    try {
      if (!camera.current) {
        console.error('Camera reference not available.');
        return;
      }

      const photo = await camera.current.takePhoto();
      console.log(photo);
      
      if (photo) {
        setCapturedPhoto(`file://${photo.path}`);
        setShowPreview(true);
		// const faces = FaceDetection.detect(`file://${photo.path}`, { landmarkMode: 'all' });
		// console.error("faces",faces)
      } else {
        console.error('Photo captured is undefined or empty.');
      }
    } catch (error) {
      console.error('Error capturing photo:', error);
    }
  };

  const confirmPhoto = () => {
    console.log('Photo confirmed:', capturedPhoto);
    setShowPreview(false);
  };

  const retakePhoto = () => {
    setCapturedPhoto(null);
    setShowPreview(false);
  };

  return (
    <View style={{ flex: 1 }}>
      <Camera
        style={{ flex: 1 }}
        device={device}
        isActive={true}
        ref={camera}
        photo={true}
        video={true}
		frameProcessor={frameProcessor}
      />
      {showPreview && capturedPhoto ? (
        <View style={{ flex: 1, justifyContent: 'center', alignItems: 'center' }}>
          <Image
            source={{ uri: capturedPhoto }}
            style={{ width: 300, height: 300, marginBottom: 20 }}
          />
          <View style={{ flexDirection: 'row', justifyContent: 'space-between' }}>
            <Button title="Retake" onPress={retakePhoto} />
            <Button title="Confirm" onPress={confirmPhoto} />
          </View>
        </View>
      ) : (
        <View style={{ flexDirection: 'row', justifyContent: 'space-evenly' }}>
          <Button title="Take Photo" onPress={takePhoto} />
		  <Image source={{uri:'https://sumedang.radarbandung.id/wp-content/uploads/sites/2/2024/03/WhatsApp-Image-2024-03-17-at-19.30.39.jpeg'}} style={{height:50,width:50}}/>
        </View>
      )}
    </View>
  );
};

export default FaceScanner;


```




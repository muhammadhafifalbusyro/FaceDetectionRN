# FaceDetectionRN

## Instalation

### 1.Instalation react-native-vision-camera (Lihat Dokumentasi)

Handling permissionn ketika klik button di halaman sebelumnya

```
const requestCameraPermission = useCallback(async () => {
		console.log('Requesting camera permission...')
		const permission = await Camera.requestCameraPermission()
		console.log(`Camera permission status: ${permission}`)
	
		if (permission === 'denied') await Linking.openSettings()
		else navigation.navigate('FACE_SCANNER_SCREEN',{permission})

	  }, [])

	const handlerNavigateScanner = () => requestCameraPermission()
```

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

Jika dengan face detection option
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
		performanceMode: 'fast', // 'fast' or 'accurate'
		landmarkMode: 'all', // 'none' or 'all'
		// classificationMode: 'none', // 'none' or 'all'
		// minFaceSize: 0.15, // Minimum face size as a proportion of the frame size
		tracking: true, 
	}).current

	const { detectFaces } = useFaceDetector(faceDetectionOptions)

	//   const handleDetectedFaces = Worklets.createRunOnJS((faces) => { 
	//     console.log('faces detected', faces)
	//   })		

	const frameProcessor = useFrameProcessor((frame) => {
		'worklet'
		console.log(`Frame: ${frame.width}x${frame.height} (${frame.pixelFormat})`)
		const faces = detectFaces(frame)
		// // ... chain frame processors
		// // ... do something with frame
		// // handleDetectedFaces(faces)
		console.log('faces detected', faces);
		console.log('eye', faces?.[0]?.landmarks?.RIGHT_EYE);

		
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

Finishing

```
import React, { useState, useRef } from 'react';
import { Text, View, StyleSheet } from 'react-native';
import { Camera, useCameraDevice, useFrameProcessor } from 'react-native-vision-camera';
import { useFaceDetector } from 'react-native-vision-camera-face-detector';

const FaceScanner = ({ navigation, route }) => {
    const [cameraPermission] = useState(route.params.permission);
    const device = useCameraDevice('front');
    const camera = useRef(null);
    const [capturedPhoto, setCapturedPhoto] = useState(null);
    const [showPreview, setShowPreview] = useState(false);
    const [landmark, setLandmark] = useState({
        "MOUTH_LEFT": { "x": 203.50892639160156, "y": 389.0068359375 },
        "LEFT_CHEEK": { "x": 178.63072204589844, "y": 350.8277893066406 },
        "MOUTH_RIGHT": { "x": 275.1216735839844, "y": 383.7560729980469 },
        "RIGHT_EAR": { "x": 332.82806396484375, "y": 316.36639404296875 },
        "LEFT_EAR": { "x": 142.35089111328125, "y": 323.4137878417969 },
        "RIGHT_CHEEK": { "x": 298.82220458984375, "y": 344.9834899902344 },
        "RIGHT_EYE": { "x": 281.5926513671875, "y": 285.391845703125 },
        "MOUTH_BOTTOM": { "x": 240.29600524902344, "y": 403.2716979980469 },
        "NOSE_BASE": { "x": 240.1834716796875, "y": 330.9016418457031 },
        "LEFT_EYE": { "x": 195.64810180664062, "y": 290.4703063964844 }
    });

    const faceDetectionOptions = useRef({
        performanceMode: 'fast',
        landmarkMode: 'all',
        tracking: true,
    }).current;

    const { detectFaces } = useFaceDetector(faceDetectionOptions);

    const frameProcessor = useFrameProcessor((frame) => {
        'worklet';
        const faces = detectFaces(frame);
    
        const centerArea = {
            width: frame.width * 0.5,
            height: frame.height * 0.5,
        };
    
        const centerStartX = (frame.width - centerArea.width) / 2;
        const centerStartY = (frame.height - centerArea.height) / 2;
        const centerEndX = centerStartX + centerArea.width;
        const centerEndY = centerStartY + centerArea.height;
    
        const centerFaces = faces.filter(face => {
            const faceCenterX = (face.bounds.x + face.bounds.width / 2);
            const faceCenterY = (face.bounds.y + face.bounds.height / 2);
    
            return (
                faceCenterX >= centerStartX &&
                faceCenterX <= centerEndX &&
                faceCenterY >= centerStartY &&
                faceCenterY <= centerEndY
            );
        });
    
        if (centerFaces.length > 0) {
            console.log('Face detected in the center area:', JSON.stringify(centerFaces, null, 2));
    
            // Membandingkan landmarks
            const faceMatched = centerFaces.some(detectedFace => {
                return Object.keys(landmark).every(key => {
                    const detectedLandmark = detectedFace.landmarks[key];
                    const defaultLandmark = landmark[key];
                    
                    // Toleransi untuk perbandingan
                    const tolerance = 10;
    
                    return (
                        Math.abs(detectedLandmark.x - defaultLandmark.x) < tolerance &&
                        Math.abs(detectedLandmark.y - defaultLandmark.y) < tolerance
                    );
                });
            });
    
            if (faceMatched) {
                console.error('Cocok');
            }
        }
    }, []);

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
            } else {
                console.error('Photo captured is undefined or empty.');
            }
        } catch (error) {
            console.error('Error capturing photo:', error);
        }
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
            {/* Frame yang menampilkan bingkai persegi dengan sudut di setiap pojok */}
            <View style={styles.frameContainer}>
                <View style={styles.cornerTopLeft} />
                <View style={styles.cornerTopRight} />
                <View style={styles.cornerBottomLeft} />
                <View style={styles.cornerBottomRight} />
            </View>
        </View>
    );
};

const styles = StyleSheet.create({
    frameContainer: {
        position: 'absolute',
        top: '25%',
        left: '25%',
        width: '50%',
        height: '50%',
        justifyContent: 'center',
        alignItems: 'center',
    },
    cornerTopLeft: {
        position: 'absolute',
        top: 0,
        left: 0,
        width: 50,
        height: 50,
        borderLeftWidth: 4,
        borderTopWidth: 4,
        borderColor: 'white',
    },
    cornerTopRight: {
        position: 'absolute',
        top: 0,
        right: 0,
        width: 50,
        height: 50,
        borderRightWidth: 4,
        borderTopWidth: 4,
        borderColor: 'white',
    },
    cornerBottomLeft: {
        position: 'absolute',
        bottom: 0,
        left: 0,
        width: 50,
        height: 50,
        borderLeftWidth: 4,
        borderBottomWidth: 4,
        borderColor: 'white',
    },
    cornerBottomRight: {
        position: 'absolute',
        bottom: 0,
        right: 0,
        width: 50,
        height: 50,
        borderRightWidth: 4,
        borderBottomWidth: 4,
        borderColor: 'white',
    },
});

export default FaceScanner;
```







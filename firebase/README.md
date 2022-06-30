알림 권한 체크
```ts
Notification.requestPermission().then((permission) => {
  if (permission === 'granted') {
    console.log('허용.')
  } else {
    console.log('설정안됨')
  }
})
```

Notification 알림 오픈
```ts
new Notification('tt', {
  body: 'bbb',
  data: 'ddd',
})
```

파이어베이스 권한체크
```ts
getToken(messaging, { vapidKey })
  .then((currentToken) => {
    if (currentToken) {
      console.log('현재 토큰', currentToken)
      // Todo: 스토어에 토큰 저장
    } else {
      console.log('토큰 없음')
    }
  })
  .catch((err) => {
    console.log('토큰 가져오기 오류', err)
  })
```

현재 권한 체크함수
```ts
function getNotificationPermissionState() {
  if (navigator.permissions) {
    return navigator.permissions.query({ name: 'notifications' }).then((result) => {
      return result.state
    })
  }

  return new Promise((resolve) => {
    resolve(Notification.permission)
  })
}
```

모듈방식 서비스워커 오픈
```
if ('serviceWorker' in navigator) {
  navigator.serviceWorker
    .register('/firebase-messaging-sw.js', { type: 'module' })
    .then((registration) => {
      console.log('서비스워커 등록 성공, scope is:', registration.scope)

      getToken(messaging, { vapidKey, serviceWorkerRegistration: registration })
        .then((currentToken) => {
          if (currentToken) {
            console.log('현재 토큰', currentToken)
            // Todo: 스토어에 토큰 저장
          } else {
            console.log('토큰 없음')
          }
        })
        .catch((err) => {
          console.log('토큰 가져오기 오류', err)
        })
    })
    .catch((err) => {
      console.log('서비스워커 등록 실패, error:', err)
    })
}
```


모듈 방식
`firebase-messaging-sw.js`
```ts
import { initializeApp } from 'firebase/app';
import { isSupported, getMessaging } from 'firebase/messaging';
import { onBackgroundMessage } from 'firebase/messaging/sw';

const firebaseApp = initializeApp({
    apiKey: 'AIzaSyA2tgIPHvYNKdUhGrtwmY6A4j4TkSa_3u4',
    authDomain: 'ceo-beta-bb257.firebaseapp.com',
    projectId: 'ceo-beta-bb257',
    storageBucket: 'ceo-beta-bb257.appspot.com',
    messagingSenderId: '88487066310',
    appId: '1:88487066310:web:236af93f905000a95e75c7',
    measurementId: 'G-E0FDBBKYT5'
});
(async () => {
    const supported = await isSupported();
    console.log(supported);
})();

const messaging = getMessaging(firebaseApp);

onBackgroundMessage(messaging, (payload) => {
    console.log('[firebase-messaging-sw.js] Received background message ', payload);

    const notificationTitle = 'Background Message Title';
    const notificationOptions = {
        body: 'Background Message body.',
        icon: '/firebase-logo.png'
    };

    self.registration.showNotification(notificationTitle, notificationOptions);
});
```

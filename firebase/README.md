`firebase-messaging-sw.js`

모듈 방식
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

import React, { useState, useEffect } from 'react'; import { View, Text, TextInput, Button, FlatList } from 'react-native'; import { initializeApp } from 'firebase/app'; import { getFirestore, collection, addDoc, onSnapshot } from 'firebase/firestore'; import { getAuth, signInAnonymously } from 'firebase/auth';

const firebaseConfig = { apiKey: 'YOUR_API_KEY', authDomain: 'YOUR_AUTH_DOMAIN', projectId: 'YOUR_PROJECT_ID', storageBucket: 'YOUR_STORAGE_BUCKET', messagingSenderId: 'YOUR_MESSAGING_SENDER_ID', appId: 'YOUR_APP_ID' };

const app = initializeApp(firebaseConfig); const db = getFirestore(app); const auth = getAuth(app);

export default function StudentShareApp() { const [message, setMessage] = useState(''); const [messages, setMessages] = useState([]);

useEffect(() => { signInAnonymously(auth);

const unsubscribe = onSnapshot(collection(db, 'messages'), (snapshot) => {
  setMessages(snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() })));
});
return unsubscribe;

}, []);

const sendMessage = async () => { if (message.trim() === '') return; await addDoc(collection(db, 'messages'), { text: message, timestamp: Date.now() }); setMessage(''); };

return ( <View style={{ flex: 1, padding: 20 }}> <FlatList data={messages} keyExtractor={(item) => item.id} renderItem={({ item }) => <Text>{item.text}</Text>} /> <TextInput value={message} onChangeText={setMessage} placeholder='Type a message...' style={{ borderWidth: 1, padding: 10, marginBottom: 10 }} /> <Button title='Send' onPress={sendMessage} /> </View> ); }



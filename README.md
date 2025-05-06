// This is a simplified React + Firebase edtech platform
// Set up: create a React app (using create-react-app) and configure Firebase

import React, { useState, useEffect } from 'react';
import { initializeApp } from 'firebase/app';
import { getFirestore, collection, getDocs, addDoc } from 'firebase/firestore';
import { Button, Card, CardContent, Input } from '@/components/ui';

const firebaseConfig = {
  apiKey: 'YOUR_API_KEY',
  authDomain: 'YOUR_AUTH_DOMAIN',
  projectId: 'YOUR_PROJECT_ID',
  storageBucket: 'YOUR_STORAGE_BUCKET',
  messagingSenderId: 'YOUR_SENDER_ID',
  appId: 'YOUR_APP_ID',
};

const app = initializeApp(firebaseConfig);
const db = getFirestore(app);

export default function EdTechPlatform() {
  const [courses, setCourses] = useState([]);
  const [newCourse, setNewCourse] = useState('');

  useEffect(() => {
    const fetchCourses = async () => {
      const courseCollection = collection(db, 'courses');
      const courseSnapshot = await getDocs(courseCollection);
      setCourses(courseSnapshot.docs.map(doc => ({ id: doc.id, ...doc.data() })));
    };
    fetchCourses();
  }, []);

  const addCourse = async () => {
    if (newCourse.trim()) {
      const docRef = await addDoc(collection(db, 'courses'), { name: newCourse });
      setCourses([...courses, { id: docRef.id, name: newCourse }]);
      setNewCourse('');
    }
  };

  return (
    <div className="p-6 max-w-2xl mx-auto">
      <h1 className="text-2xl font-bold mb-4">EdTech Platform</h1>
      <div className="mb-4 flex gap-2">
        <Input
          placeholder="New course name"
          value={newCourse}
          onChange={e => setNewCourse(e.target.value)}
        />
        <Button onClick={addCourse}>Add Course (Admin)</Button>
      </div>
      <div className="grid gap-4">
        {courses.map(course => (
          <Card key={course.id}>
            <CardContent>
              <h2 className="text-xl font-semibold">{course.name}</h2>
              <Button>Enroll</Button>
            </CardContent>
          </Card>
        ))}
      </div>
    </div>
  );
}

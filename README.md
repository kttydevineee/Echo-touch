# Echo-touch
Women’s sexual wellness app 
MIT License

Copyright (c) 2025 kttydevineee

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
# Echo Touch — Architecture (summary)

Overview
- Mobile (Expo + React Native): onboarding, device pairing, session manager, local analytics, audio engine, vibrator control, in-app store UI (placeholder).
- Server (FastAPI): optional cloud model training, session storage (opt-in), purchase & order backend (placeholder).
- ML: prototype for time-series feature extraction and a simple classifier/regressor; designed for on-device inference (TFLite) and optional aggregated cloud training (opt-in).

Core design goals
- Local-first: do inference on-device by default.
- Privacy: explicit consent, minimal PII, encryption.
- Safety: intensity limits, emergency stop, warnings for specific conditions.
- Extensibility: BLE GATT API for vibrator, firmware OTA flow (spec stub).

Files
- mobile/: React Native app (Expo)
- server/: FastAPI server + predictor prototype
- docs: privacy_policy.md, store/README.md, ARCHITECTURE.md
- # Privacy & Safety — Echo Touch (Draft)

This is a draft privacy & safety document for Echo Touch. This document is not legal advice—consult counsel for final policies.

Principles
- Explicit consent before collecting physiological/sexual wellness data.
- Local-first inference and storage; cloud upload only if user opts in.
- Minimal PII collection and strong encryption for stored data.
- Age verification and content consent before enabling sexual features.
- Clear "delete my data" flow.

Data categories
- PII: name, email (optional)
- Payments: payment & order history
- Physiological signals: HR, HRV, breathing, accelerometer (session data)
- Session summaries & personalization parameters

Retention
- Default short retention (30–90 days). Allow user to extend or delete.

Third parties
- Payments: Stripe (placeholders). Don't store payment card details directly.
- Analytics: opt-in only.

Safety disclaimer
- Echo Touch is a wellness product, not a medical device. Recommend consulting clinicians for persistent issues. Provide content restrictions for users with pacemakers, pregnancy, or severe cardiac conditions.

- name: CI

on:
  push:
    branches:
      - main
      - echo-touch/initial
  pull_request:

jobs:
  mobile:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: '18'
      - name: Install mobile deps
        working-directory: mobile
        run: |
          npm ci
      - name: TypeScript check
        working-directory: mobile
        run: npm run type-check || true

  server:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Setup Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.10'
      - name: Install server deps
        working-directory: server
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt
      - name: Run predictor demo
        working-directory: server
        run: |
          python analysis/predictor.py || true
          {
  "name": "echo-touch-mobile",
  "version": "0.1.0",
  "private": true,
  "main": "node_modules/expo/AppEntry.js",
  "scripts": {
    "start": "expo start",
    "android": "expo run:android",
    "ios": "expo run:ios",
    "web": "expo start --web",
    "type-check": "tsc --noEmit"
  },
  "dependencies": {
    "expo": "~48.0.0",
    "expo-av": "~13.0.0",
    "react": "18.2.0",
    "react-native": "0.71.8",
    "react-native-ble-plx": "^2.0.2"
  },
  "devDependencies": {
    "typescript": "^4.9.5"
  }
}
- {
  "expo": {
    "name": "Echo Touch",
    "slug": "echo-touch",
    "version": "0.1.0",
    "platforms": ["ios", "android"],
    "sdkVersion": "48.0.0"
  }
  {
  "compilerOptions": {
    "target": "ES2019",
    "module": "ESNext",
    "jsx": "react",
    "moduleResolution": "node",
    "allowSyntheticDefaultImports": true,
    "esModuleInterop": true,
    "strict": false,
    "skipLibCheck": true,
    "resolveJsonModule": true,
    "noEmit": true
  },
  "exclude": ["node_modules"]
import React from "react";
import { SafeAreaView, View, Text, Button, StyleSheet, ScrollView } from "react-native";
import BreathCoach from "./components/BreathCoach";
import BluetoothVibe from "./components/BluetoothVibe";

export default function App() {
  const [sessionActive, setSessionActive] = React.useState(false);

  return (
    <SafeAreaView style={styles.container}>
      <ScrollView contentContainerStyle={{ padding: 20 }}>
        <Text style={styles.title}>Echo Touch</Text>
        <Text style={styles.subtitle}>Personalized breathwork & body analytics</Text>

        <View style={styles.section}>
          <Button
            title={sessionActive ? "Stop Session" : "Start Session"}
            onPress={() => setSessionActive(!sessionActive)}
          />
        </View>

        {sessionActive && (
          <>
            <BreathCoach />
            <BluetoothVibe />
          </>
        )}

        <View style={{ marginTop: 20 }}>
          <Text style={styles.small}>
            Note: Prototype for demonstration. Physiological data requires explicit user consent.
          </Text>
        </View>
      </ScrollView>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: { flex: 1, backgroundColor: "#fff" },
  title: { fontSize: 28, fontWeight: "700" },
  subtitle: { fontSize: 14, marginTop: 4, color: "#666" },
  section: { marginTop: 18 },
  small: { fontSize: 12, color: "#888" }
});
import React from "react";
import { View, Text, Button, StyleSheet } from "react-native";
import { Audio } from "expo-av";

/**
 * Simple breath coach: plays timed inhale/exhale cues and a carrier tone.
 * Use public-domain sample audio placed in mobile/assets/tones/
 */

export default function BreathCoach() {
  const [running, setRunning] = React.useState(false);
  const soundRef = React.useRef<Audio.Sound | null>(null);

  React.useEffect(() => {
    return () => {
      if (soundRef.current) {
        soundRef.current.unloadAsync();
      }
    };*


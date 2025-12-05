## Hi there 👋

<!--
**brika-digital/brika-digital** is a ✨ _special_ ✨ repository because its `README.md` (this file) appears on your GitHub profile.

Here are some ideas to get you started:

- 🔭 I’m currently working on ...
- 🌱 I’m currently learning ...
- 👯 I’m looking to collaborate on ...
- 🤔 I’m looking for help with ...
- 💬 Ask me about ...
- 📫 How to reach me: ...
- 😄 Pronouns: ...
- ⚡ Fun fact: ...
-->
========================================
FILE: App.js
========================================
import React, { useEffect, useState } from "react";
import { NavigationContainer } from "@react-navigation/native";
import { createStackNavigator } from "@react-navigation/stack";
import LoginScreen from "./src/screens/LoginScreen";
import RegisterScreen from "./src/screens/RegisterScreen";
import HomeClient from "./src/screens/client/HomeClient";
import HomeOwner from "./src/screens/owner/HomeOwner";
import AddRoom from "./src/screens/owner/AddRoom";
import BookRoom from "./src/screens/client/BookRoom";
import AsyncStorage from "@react-native-async-storage/async-storage";

const Stack = createStackNavigator();

export default function App() {
  const [role, setRole] = useState(null);

  useEffect(() => {
    const loadRole = async () => {
      const saved = await AsyncStorage.getItem("role");
      if (saved) setRole(saved);
    };
    loadRole();
  }, []);

  return (
    <NavigationContainer>
      <Stack.Navigator screenOptions={{ headerShown: false }}>
        {!role && (
          <>
            <Stack.Screen name="Login" component={LoginScreen} />
            <Stack.Screen name="Register" component={RegisterScreen} />
          </>
        )}

        {role === "client" && (
          <>
            <Stack.Screen name="HomeClient" component={HomeClient} />
            <Stack.Screen name="BookRoom" component={BookRoom} />
          </>
        )}

        {role === "owner" && (
          <>
            <Stack.Screen name="HomeOwner" component={HomeOwner} />
            <Stack.Screen name="AddRoom" component={AddRoom} />
          </>
        )}
      </Stack.Navigator>
    </NavigationContainer>
  );
}

========================================
FILE: app.json
========================================
{
  "expo": {
    "name": "BrikaDigital",
    "slug": "brika-digital",
    "version": "1.0.0",
    "orientation": "portrait",
    "sdkVersion": "51.0.0",
    "platforms": ["android", "ios"],
    "assetBundlePatterns": ["**/*"]
  }
}

========================================
FILE: src/utils/data.js
(قاعدة بيانات بسيطة داخل التطبيق)
========================================
export const ROOMS = [];
export const BOOKINGS = [];

========================================
FILE: src/screens/LoginScreen.js
========================================
import React, { useState } from "react";
import { View, Text, TextInput, TouchableOpacity } from "react-native";
import AsyncStorage from "@react-native-async-storage/async-storage";

export default function LoginScreen({ navigation }) {
  const [email, setEmail] = useState("");

  const login = async () => {
    if (email.includes("owner")) {
      await AsyncStorage.setItem("role", "owner");
      navigation.replace("HomeOwner");
    } else {
      await AsyncStorage.setItem("role", "client");
      navigation.replace("HomeClient");
    }
  };

  return (
    <View style={{ flex: 1, padding: 22, justifyContent: "center" }}>
      <Text style={{ fontSize: 28, fontWeight: "bold" }}>تسجيل الدخول</Text>

      <TextInput
        placeholder="البريد الإلكتروني"
        onChangeText={setEmail}
        style={{ borderWidth: 1, marginTop: 20, padding: 10 }}
      />

      <TouchableOpacity
        onPress={login}
        style={{
          marginTop: 20,
          padding: 15,
          backgroundColor: "black",
        }}
      >
        <Text style={{ color: "white", textAlign: "center" }}>دخول</Text>
      </TouchableOpacity>

      <TouchableOpacity onPress={() => navigation.navigate("Register")}>
        <Text style={{ marginTop: 20, textAlign: "center" }}>
          ليس لديك حساب؟ سجل الآن
        </Text>
      </TouchableOpacity>
    </View>
  );
}

========================================
FILE: src/screens/RegisterScreen.js
========================================
import React, { useState } from "react";
import { View, Text, TextInput, TouchableOpacity } from "react-native";

export default function RegisterScreen({ navigation }) {
  return (
    <View style={{ flex: 1, padding: 22, justifyContent: "center" }}>
      <Text style={{ fontSize: 28, fontWeight: "bold" }}>إنشاء حساب</Text>

      <TextInput
        placeholder="الإسم"
        style={{ borderWidth: 1, marginTop: 20, padding: 10 }}
      />

      <TextInput
        placeholder="البريد الإلكتروني"
        style={{ borderWidth: 1, marginTop: 20, padding: 10 }}
      />

      <TouchableOpacity
        style={{
          marginTop: 20,
          padding: 15,
          backgroundColor: "black",
        }}
        onPress={() => navigation.navigate("Login")}
      >
        <Text style={{ color: "white", textAlign: "center" }}>تسجيل</Text>
      </TouchableOpacity>
    </View>
  );
}

========================================
FILE: src/screens/client/HomeClient.js
========================================
import React from "react";
import { View, Text, TouchableOpacity, FlatList } from "react-native";
import { ROOMS } from "../../utils/data";

export default function HomeClient({ navigation }) {
  return (
    <View style={{ flex: 1, padding: 22 }}>
      <Text style={{ fontSize: 26, fontWeight: "bold" }}>الغرف المتاحة</Text>

      <FlatList
        data={ROOMS}
        keyExtractor={(i) => i.id}
        renderItem={({ item }) => (
          <TouchableOpacity
            onPress={() => navigation.navigate("BookRoom", { room: item })}
            style={{
              padding: 15,
              borderWidth: 1,
              marginTop: 15,
            }}
          >
            <Text>{item.name}</Text>
            <Text>السعر: {item.price} دينار</Text>
          </TouchableOpacity>
        )}
      />
    </View>
  );
}

========================================
FILE: src/screens/client/BookRoom.js
========================================
import React from "react";
import { View, Text, TouchableOpacity } from "react-native";
import { BOOKINGS } from "../../utils/data";

export default function BookRoom({ route, navigation }) {
  const { room } = route.params;

  const reserve = () => {
    BOOKINGS.push({
      id: Date.now().toString(),
      room: room.name,
      price: room.price,
    });
    alert("تم الحجز بنجاح");
    navigation.goBack();
  };

  return (
    <View style={{ flex: 1, padding: 22 }}>
      <Text style={{ fontSize: 26 }}>{room.name}</Text>
      <Text style={{ marginTop: 10 }}>السعر: {room.price} دينار</Text>

      <TouchableOpacity
        onPress={reserve}
        style={{ marginTop: 20, backgroundColor: "black", padding: 15 }}
      >
        <Text style={{ color: "white", textAlign: "center" }}>حجز</Text>
      </TouchableOpacity>
    </View>
  );
}

========================================
FILE: src/screens/owner/HomeOwner.js
========================================
import React from "react";
import { View, Text, TouchableOpacity } from "react-native";

export default function HomeOwner({ navigation }) {
  return (
    <View style={{ flex: 1, padding: 22 }}>
      <Text style={{ fontSize: 28, fontWeight: "bold" }}>
        لوحة المالك
      </Text>

      <TouchableOpacity
        style={{ marginTop: 20, padding: 15, borderWidth: 1 }}
        onPress={() => navigation.navigate("AddRoom")}
      >
        <Text>إضافة غرفة جديدة</Text>
      </TouchableOpacity>
    </View>
  );
}

========================================
FILE: src/screens/owner/AddRoom.js
========================================
import React, { useState } from "react";
import { View, Text, TextInput, TouchableOpacity } from "react-native";
import { ROOMS } from "../../utils/data";

export default function AddRoom({ navigation }) {
  const [name, setName] = useState("");
  const [price, setPrice] = useState("");

  const addRoom = () => {
    ROOMS.push({
      id: Date.now().toString(),
      name,
      price,
    });
    alert("تمت إضافة الغرفة");
    navigation.goBack();
  };

  return (
    <View style={{ flex: 1, padding: 22 }}>
      <Text style={{ fontSize: 26, fontWeight: "bold" }}>إضافة غرفة</Text>

      <TextInput
        placeholder="اسم الغرفة"
        onChangeText={setName}
        style={{ borderWidth: 1, marginTop: 20, padding: 10 }}
      />

      <TextInput
        placeholder="السعر"
        onChangeText={setPrice}
        keyboardType="numeric"
        style={{ borderWidth: 1, marginTop: 20, padding: 10 }}
      />

      <TouchableOpacity
        onPress={addRoom}
        style={{ marginTop: 20, backgroundColor: "black", padding: 15 }}
      >
        <Text style={{ color: "white", textAlign: "center" }}>حفظ</Text>
      </TouchableOpacity>
    </View>
  );
}

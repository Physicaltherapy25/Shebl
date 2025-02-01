<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>تسجيل الدخول - Shebl</title>
    <link rel="stylesheet" href="style.css">
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-app.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-auth.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.6.1/firebase-database.js"></script>
</head>
<body>
    <header>
        <img src="logo.png" alt="شعار Shebl" class="logo">
        <h1>Shebl</h1>
        <p>تطبيق يربط مرضى العلاج الطبيعي بأفضل الأطباء</p>
    </header>
    <div class="container">
        <h2>تسجيل الدخول</h2>
        <form id="loginForm">
            <label for="email">البريد الإلكتروني:</label>
            <input type="email" id="email" required>
            <label for="password">كلمة المرور:</label>
            <input type="password" id="password" required>
            <button type="submit">تسجيل الدخول</button>
        </form>
        <p>ليس لديك حساب؟ <a href="register.html">إنشاء حساب جديد</a></p>
    </div>
    <script src="firebase-config.js"></script>
    <script src="auth.js"></script>
</body>
</html>
<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>إنشاء حساب - Shebl</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <header>
        <img src="logo.png" alt="شعار Shebl" class="logo">
        <h1>إنشاء حساب جديد</h1>
    </header>
    <div class="container">
        <h2>معلومات الحساب</h2>
        <form id="registerForm">
            <label for="userType">نوع الحساب:</label>
            <select id="userType">
                <option value="patient">مريض</option>
                <option value="doctor">طبيب</option>
            </select>
            <label for="name">الاسم الكامل:</label>
            <input type="text" id="name" required>
            <label for="email">البريد الإلكتروني:</label>
            <input type="email" id="email" required>
            <label for="password">كلمة المرور:</label>
            <input type="password" id="password" required>
            <label for="confirmPassword">تأكيد كلمة المرور:</label>
            <input type="password" id="confirmPassword" required>
            <label for="certificate" id="certificateLabel" style="display: none;">رفع شهادة التخرج:</label>
            <input type="file" id="certificate" style="display: none;">
            <button type="submit">إنشاء حساب</button>
        </form>
        <p>لديك حساب بالفعل؟ <a href="index.html">تسجيل الدخول</a></p>
    </div>
    <script src="firebase-config.js"></script>
    <script src="register.js"></script>
</body>
</html>
body {
    background-image: url("background.jpg"); /* صورة الخلفية */
    background-size: cover; /* احتواء الصورة */
    font-family: sans-serif;
}

header {
    background-color: rgba(255, 255, 255, 0.8); /* خلفية شفافة للرأس */
    padding: 20px;
    text-align: center;
}

.logo {
    width: 100px; /* حجم الشعار */
    margin-bottom: 10px;
}

.container {
    background-color: rgba(255, 255, 255, 0.8); /* خلفية شفافة للحاوية */
    padding: 20px;
    border-radius: 10px;
    margin: 50px auto;
    width: 400px;
}

/* ... باقي أنماط CSS ... */
// ... باقي الكود ...

firebase.auth().createUserWithEmailAndPassword(email, password)
    .then((userCredential) => {
        let userId = userCredential.user.uid;
        let userData = {
            name,
            email,
            userType
        };

        // إضافة اسم المستخدم إلى البيانات
        if (userType === "doctor") {
            let certificate = document.getElementById("certificate").files[0];
            if (certificate) {
                let storageRef = firebase.storage().ref('certificates/' + userId);
                storageRef.put(certificate).then(() => {
                    userData.certificateUploaded = true;
                    firebase.database().ref('users/' + userId).set(userData);
                });
            }
        } else {
            firebase.database().ref('users/' + userId).set(userData);
        }

        alert("تم إنشاء الحساب بنجاح!");
        window.location.href = "index.html";
    })
    .catch(error => alert(error.message));
});
// ... باقي الكود ...

firebase.auth().signInWithEmailAndPassword(email, password)
    .then((userCredential) => {
        let user = userCredential.user;
        // جلب بيانات المستخدم من قاعدة البيانات
        firebase.database().ref('users/' + user.uid).once('value', (snapshot) => {
            let userData = snapshot.val();
            if (userData.userType === 'doctor') {
                window.location.href = "doctor-dashboard.html"; // صفحة لوحة تحكم الطبيب
            } else {
                window.location.href = "patient-dashboard.html"; // صفحة لوحة تحكم المريض
            }
        });
    })
    .catch(error => alert(error.message));
});

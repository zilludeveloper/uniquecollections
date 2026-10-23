# বাংলা ই-কমার্স (ক্যাশ অন ডেলিভারি) — সেটআপ গাইড

GitHub Pages শুধু স্ট্যাটিক ফাইল চালায়, তাই অর্ডার/পণ্য/ইউজার রাখতে **Firebase (ফ্রি)** ব্যবহার হয়েছে।

## ১. Firebase সেটআপ (৫ মিনিট)
1. https://console.firebase.google.com → **Add project**
2. **Build → Authentication → Sign-in method →** Email/Password চালু করুন
3. **Build → Firestore Database → Create database** (Production mode)
4. **Rules** ট্যাবে নিচের কোড পেস্ট করে Publish করুন (ইমেইল বদলান):

```
rules_version = '2';
service cloud.firestore {
  match /databases/{d}/documents {
    function admin() {
      return request.auth != null && request.auth.token.email in ['your-email@gmail.com'];
    }
    match /products/{id} { allow read: if true; allow write: if admin(); }
    match /settings/{id} { allow read: if true; allow write: if admin(); }
    match /orders/{id} {
      allow create: if request.resource.data.status == 'new' && request.resource.data.total is number;
      allow read: if admin() || (request.auth != null && resource.data.uid == request.auth.uid);
      allow update, delete: if admin();
    }
  }
}
```
5. **Project settings → Your apps → Web (</>)** থেকে config কপি করে `index.html` এর `C.firebase` এ বসান এবং `admins` এ আপনার ইমেইল দিন।

## ২. GitHub এ আপলোড
1. নতুন repository খুলে `index.html` আপলোড করুন
2. **Settings → Pages → Branch: main / root → Save**
3. সাইটের লিংক (`username.github.io/repo`) Firebase **Authentication → Settings → Authorized domains** এ যোগ করুন

## ৩. ব্যবহার
- আগে **নিজের অ্যাডমিন ইমেইল দিয়ে সাইন আপ** করুন (অন্য কেউ যেন আগে না করে), তারপর মেনুতে **ড্যাশবোর্ড** দেখবেন।
- **পণ্য:** নাম, দাম, ছবির লিংক (imgbb.com এ ছবি আপলোড করে লিংক নিন), বিবরণ দিয়ে পণ্য যোগ/এডিট/ডিলিট।
  প্রতিটি পণ্যের নিজস্ব ল্যান্ডিং পেজ থাকে যেখানেই সরাসরি অর্ডার ফর্ম আছে।
- **অর্ডার:** স্ট্যাটাস পরিবর্তন, সার্চ/ফিল্টার, ইনভয়েস প্রিন্ট, CSV ডাউনলোড, ডিলিট।
- **সেটিংস:** দোকানের নাম, ফোন, ফেসবুক লিংক, ডেলিভারি চার্জ (ঢাকা ৭০ / বাইরে ১৩০) কোড না ছুঁয়েই বদলানো যায়।

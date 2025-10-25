### 🧩 **String Input and Handling**

`cin >> s;              // takes input without whitespace getline(cin, s);       // takes full line input (with spaces)  cin.ignore();          // fixes newline issue before getline()  // taking multiple full-line inputs int k; cin >> k; cin.ignore();          // ignore leftover newline while(k--) {     string line;     getline(cin, line);     cout << line << endl; }`

---

### 🔠 **String Comparison**

`string s1 = "AdiBul", s2 = "Adibul"; cout << (s1 >= s2) << endl;   // lexicographical comparison`

---

### ✂️ **Substring Extraction**

`string t = s1.substr(1, 3);   // from index 1, take 3 characters`

---

### 🔤 **Sorting Strings**

`sort(s1.begin(), s1.end());       // sort full string sort(s2.begin(), s2.begin()+3);   // sort first 3 characters`

---

### 🧮 **Counting in Strings**

`// count digits int cnt = 0; for(char c: s)     if(c >= '0' && c <= '9') cnt++;  // count frequency of letters vector<int> v(26, 0); for(char c: k) v[c - 'a']++;`

---

### 🔁 **Check for Anagram**

`// sort both strings and compare sort(a.begin(), a.end()); sort(b.begin(), b.end()); if(a == b) cout << "Anagram";`

---

### 💬 **Special Characters**

`string s = "This is a backslash: \\"; string k = "This is a double quote: \"\"";`

---

1. Taking Inputs

```cpp
        string s;

        // no whitespace

        //cin >> s;

  

        // taking input of full line

        getline(cin,s);

  

        //getting multiple full lines

        int k; cin >> k;

        while(k--){

            //solving white space issues

            // char c; cin >> c; //doing this

            //Alternatively cin.ignore

            cin.ignore();

  
  

            //doing this ignores 1st line as getline(cin,s) consumes the newline

            string line;

            getline(cin,line);

            cout << line << endl;

        }
```


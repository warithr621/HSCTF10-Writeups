# rev/mystery-methods

## Problem: 

vqx jung gb fnl urer

[`mysteryMethods.java`](https://hsctf-10-resources.storage.googleapis.com/uploads/8f40697b942f04374a3860cc054c8704ad338392b7df6a0511da2fd49d18c9f4/mysteryMethods.java)

## Solution: 

Yet another Java file, so let's outline what this program exactly does:
- Read input and stores it in `userInput`
- Encrypts this input using the `encryptInput()` method
  - runs the `unknown2()` method, which converts the input into bytes and uses Base64 encoding (this method also has another argument `integer` which does nothing
  - runs the `unknown1()` method, which reverses the string
  - once again runs the `unknown2()` method
  - runs the `unknown()` method with an additional argument 25, which upon further inspection is the Caesar shift (to the right) to shift the string
- Checks if `encryptedInput` is the flag by running it against a checker `checkFlag()`, which simply checks if the passed input is equal to some string `"OS1QYj9VaEolaDgTSTXxSWj5Uj5JNVwRUT4vX290L1ondF1z"`

Now all we have to do is write a program that reverses these steps: uses `unknown()` to shift the string 25 *left* (or 1 right), run `unknown2()` in reverse, run `unknown1()`, and then run `unknown2()` in reverse again

```Java
import java.util.*;

public class MMSolver {
    
    public static void main(String[] args) {
        String s = "OS1QYj9VaEolaDgTSTXxSWj5Uj5JNVwRUT4vX290L1ondF1z";
        s = unknown(s,1);
        s = unknown2(s);
        s = unknown1(s);
        s = unknown2(s);
        System.out.println(s);
    }
    
    public static String unknown(String input, int something) {
        // just copy this from the program
        StringBuilder result = new StringBuilder();
        for (char c : input.toCharArray()) {
            if (Character.isLetter(c)) {
                char base = Character.isUpperCase(c) ? 'A' : 'a';
                int offset = (c - base + something) % 26;
                if (offset < 0) {
                    offset += 26;
                }
                c = (char) (base + offset);
            }
            result.append(c);
        }
        return result.toString();
    }
    
    public static String unknown1(String xyz) {
        // also copy this
        return new StringBuilder(xyz).reverse().toString();
    }
    
    public static String unknown2(String xyz) {
        // actually reverse this
        return new String(Base64.getDecoder().decode(xyz));
    }
    
}
```

## Flag:

`flag{hsCTF_I5_r3aLLy_fUN}`
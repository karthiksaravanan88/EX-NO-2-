## EX. NO:2 IMPLEMENTATION OF PLAYFAIR CIPHER

 

## AIM:
To write a C program to implement the Playfair Substitution technique.

## DESCRIPTION:

The Playfair cipher starts with creating a key table. The key table is a 5×5 grid of letters that will act as the key for encrypting your plaintext. Each of the 25 letters must be unique and one letter of the alphabet is omitted from the table (as there are 25 spots and 26 letters in the alphabet).

To encrypt a message, one would break the message into digrams (groups of 2 letters) such that, for example, "HelloWorld" becomes "HE LL OW OR LD", and map them out on the key table. The two letters of the diagram are considered as the opposite corners of a rectangle in the key table. Note the relative position of the corners of this rectangle. Then apply the following 4 rules, in order, to each pair of letters in the plaintext:
1.	If both letters are the same (or only one letter is left), add an "X" after the first letter
2.	If the letters appear on the same row of your table, replace them with the letters to their immediate right respectively
3.	If the letters appear on the same column of your table, replace them with the letters immediately below respectively
4.	If the letters are not on the same row or column, replace them with the letters on the same row respectively but at the other pair of corners of the rectangle defined by the original pair.
## EXAMPLE:
![image](https://github.com/Hemamanigandan/EX-NO-2-/assets/149653568/e6858d4f-b122-42ba-acdb-db18ec2e9675)

 

## ALGORITHM:

STEP-1: Read the plain text from the user.
STEP-2: Read the keyword from the user.
STEP-3: Arrange the keyword without duplicates in a 5*5 matrix in the row order and fill the remaining cells with missed out letters in alphabetical order. Note that ‘i’ and ‘j’ takes the same cell.
STEP-4: Group the plain text in pairs and match the corresponding corner letters by forming a rectangular grid.
STEP-5: Display the obtained cipher text.




## Program:
```java
import java.util.*;

public class PlayfairCipher {

    private char[][] keyTable = new char[5][5];

    // Convert text to lowercase and remove spaces
    private String formatText(String text) {
        text = text.toLowerCase().replaceAll("[^a-z]", "");
        text = text.replace("j", "i");
        return text;
    }

    // Generate the 5x5 key table
    private void generateKeyTable(String key) {
        boolean[] used = new boolean[26];
        key = formatText(key);

        int row = 0, col = 0;

        for (char c : key.toCharArray()) {
            if (!used[c - 'a']) {
                keyTable[row][col++] = c;
                used[c - 'a'] = true;
                if (col == 5) {
                    row++;
                    col = 0;
                }
            }
        }

        for (char c = 'a'; c <= 'z'; c++) {
            if (c == 'j') continue;
            if (!used[c - 'a']) {
                keyTable[row][col++] = c;
                used[c - 'a'] = true;
                if (col == 5) {
                    row++;
                    col = 0;
                }
            }
        }
    }

    // Prepare plaintext into digraphs
    private String preparePlainText(String text) {
        text = formatText(text);
        StringBuilder sb = new StringBuilder(text);

        for (int i = 0; i < sb.length(); i += 2) {
            if (i + 1 == sb.length() || sb.charAt(i) == sb.charAt(i + 1)) {
                sb.insert(i + 1, 'x');
            }
        }

        if (sb.length() % 2 != 0) {
            sb.append('x');
        }

        return sb.toString();
    }

    // Find position of a character in key table
    private int[] findPosition(char c) {
        for (int i = 0; i < 5; i++) {
            for (int j = 0; j < 5; j++) {
                if (keyTable[i][j] == c) {
                    return new int[]{i, j};
                }
            }
        }
        return null;
    }

    // Encrypt plaintext
    public String encrypt(String plaintext, String key) {
        generateKeyTable(key);
        plaintext = preparePlainText(plaintext);

        StringBuilder cipher = new StringBuilder();

        for (int i = 0; i < plaintext.length(); i += 2) {
            char a = plaintext.charAt(i);
            char b = plaintext.charAt(i + 1);

            int[] pos1 = findPosition(a);
            int[] pos2 = findPosition(b);

            if (pos1[0] == pos2[0]) {
                cipher.append(keyTable[pos1[0]][(pos1[1] + 1) % 5]);
                cipher.append(keyTable[pos2[0]][(pos2[1] + 1) % 5]);
            } else if (pos1[1] == pos2[1]) {
                cipher.append(keyTable[(pos1[0] + 1) % 5][pos1[1]]);
                cipher.append(keyTable[(pos2[0] + 1) % 5][pos2[1]]);
            } else {
                cipher.append(keyTable[pos1[0]][pos2[1]]);
                cipher.append(keyTable[pos2[0]][pos1[1]]);
            }
        }

        return cipher.toString();
    }

    // Decrypt ciphertext
    public String decrypt(String ciphertext, String key) {
        generateKeyTable(key);
        ciphertext = formatText(ciphertext);

        StringBuilder plain = new StringBuilder();

        for (int i = 0; i < ciphertext.length(); i += 2) {
            char a = ciphertext.charAt(i);
            char b = ciphertext.charAt(i + 1);

            int[] pos1 = findPosition(a);
            int[] pos2 = findPosition(b);

            if (pos1[0] == pos2[0]) {
                plain.append(keyTable[pos1[0]][(pos1[1] + 4) % 5]);
                plain.append(keyTable[pos2[0]][(pos2[1] + 4) % 5]);
            } else if (pos1[1] == pos2[1]) {
                plain.append(keyTable[(pos1[0] + 4) % 5][pos1[1]]);
                plain.append(keyTable[(pos2[0] + 4) % 5][pos2[1]]);
            } else {
                plain.append(keyTable[pos1[0]][pos2[1]]);
                plain.append(keyTable[pos2[0]][pos1[1]]);
            }
        }

        return plain.toString();
    }

    // Display key table
    public void displayKeyTable() {
        System.out.println("Key Table:");
        for (char[] row : keyTable) {
            for (char c : row) {
                System.out.print(c + " ");
            }
            System.out.println();
        }
    }

    // Main method
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        PlayfairCipher pf = new PlayfairCipher();

        String key, plaintext;
        System.out.print("Enter the Key : ");
        key = sc.nextLine();
        System.out.print("Enter the plaintext : ");
        plaintext = sc.nextLine();

        System.out.println("************");

        System.out.println("Key Text    : " + key);
        System.out.println("Plain Text  : " + plaintext);

        String cipher = pf.encrypt(plaintext, key);
        System.out.println("Cipher Text : " + cipher);

        String decrypted = pf.decrypt(cipher, key);
        System.out.println("Decrypted Text : " + decrypted);
        sc.close();
    }
}

```



## Output:
<img width="1624" height="894" alt="image" src="https://github.com/user-attachments/assets/b08440ae-cfa6-451d-9f34-9d768053cd69" />

## RESULT :
 Thus the implementation of ceasar cipher had been executed successfully.

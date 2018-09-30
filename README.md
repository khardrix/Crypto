# Crypto
Crypto program for Microsoft course on Java: Learn to Program in Java. (Module 3)

/*********************************************************************************************************
 *********************************************************************************************************
 *****                                | MODULE PROJECT: Crypto |                                     *****
 *****-----------------------------------------------------------------------------------------------*****
 *****        For this project you will be writing methods of encrypting and decrypting text.        *****
 *********************************************************************************************************
 *********************************************************************************************************/

//IMPORTS of needed tools and plug-ins.
import java.util.Scanner;

public class MP3_Crypto {

    public static void main(String[] args){

        //INSTANCE VARIABLE(s) declaration(s).
        Scanner input = new Scanner(System.in);
        String userInputText, cypherText, plainText;
        int userShiftValue, userChunkSize, decyrptShiftValue;

        //Program explanation to user.
        System.out.println("\nWelcome to my encryption program, \"Crypto.\" " +
                "\n\nWith this program, you will be able to input text, the number of letters " +
                "\nto shift the text up or down the alphabet and the size \"chunks\" you want " +
                "\nyour encrypted text to be in. (This program will add \"x\"s to the end of the " +
                "\nlast chunk to make it the same size.) This encryption program will remove " +
                "\nany punctuation or spaces, convert all letters to uppercase letters and will also " +
                "\nadd the letters \"OB\" before every vowel that you type. ");

        //User interaction with program.
        System.out.print("\nEnter some text: ");
        userInputText = input.nextLine();

        System.out.print("\nEnter the number of alphabetical characters you want to shift " +
                "\nyour text up (POSITIVE INTEGER) or down (NEGATIVE INTEGER) the alphabet: ");
        userShiftValue = input.nextInt();

        System.out.print("\nEnter the size (WARNING: POSITIVE INTEGERS ONLY) of \"chunks\" " +
                "\n(\"3\" = \"XXX XXX\" or \"4\" = \"XXXX XXXX\") you want your text: ");
        userChunkSize = input.nextInt();

        System.out.println("\nYOUR INITIAL TEXT:    " + userInputText);

        cypherText = encryptString(userInputText, userShiftValue, userChunkSize);
        System.out.println("YOUR ENCRYPTED TEXT:    " + cypherText);

        decyrptShiftValue = userShiftValue * -1;
        plainText = decryptString(cypherText, decyrptShiftValue);
        System.out.println("YOUR DECRYPTED PLAIN TEXT:    " + plainText);
    }

    //METHOD to remove punctuation and spaces and convert everything to uppercase.
    public static String normalizeText(String str){

        if(str.length() == 0)
            return str;
        char c = str.charAt(0);
        if(str.substring(0, 1).matches("\\p{Blank}"))
            return normalizeText(str.substring(1));
        if(str.substring(0, 1).matches("\\p{Punct}"))
            return normalizeText(str.substring(1));
        if(str.substring(0, 1).matches("\\p{Lower}")){
            c -= 32;
            return c + normalizeText(str.substring(1));
        }
        return str.charAt(0) + normalizeText(str.substring(1));
    }

    //METHOD to add the letters "OB" before every vowel.
    public static String obify(String str){

        String ob = "OB";
        if(str.length() == 0)
            return str;
        char c = str.charAt(0);
        if("AEIOUY".contains(Character.toString(str.charAt(0))))
            return ob + c + obify(str.substring(1));
        return c + obify(str.substring(1));
    }

    //METHOD to shift every alphabetical character up or down the wrapped alphabet.
    public static String caesarify(String str, int key) {

        if(str.length() == 0)
            return str;
        if(key == 0)
            return str;
        key = key < 0 ? 26 - Math.abs(key) : key;
        String result = "";
        char c = str.charAt(0);
        if(result.length() < str.length()) {
            c += key;
            if(c > 'Z') {
                c -= 26;
                result += c;
                return result + caesarify(str.substring(1), key);
            }else if(c < 'A'){
                c += 26;
                result += c;
                return result + caesarify(str.substring(1), key);
            }
                result += c;
                return result + caesarify(str.substring(1), key);
        }
        return result;
    }

    //METHOD to group text into chunks of user specified size and add "x"s to end of last
        //chunk to make it the same length.
    public static String groupify(String str, int n){

      String temp = "";
      int y = 1;
      if(str.length() % n == 0){
          for(int x = 0; x != str.length(); x++){
              if(y % n == 0){
                  temp += str.charAt(x) + " ";
              }else{
                  temp += str.charAt(x);
              }
              y++;
          }
      }else{
          str += "x";
          temp = groupify(str, n);
      }
      return temp;
  }

  //METHOD to call other METHODS to encrypt text input.
  public static String encryptString(String str, int shift, int size){

        str = normalizeText(str);
        str = obify(str);
        str = caesarify(str, shift);
        str = groupify(str, size);
        return str;
    }

    //METHOD to ungroup text input.
    public static String ungroupify(String str){

        char c;
        if(str.length() == 0)
            return str;
        c = str.charAt(0);
        if(c == 'x' || c == ' ')
            return ungroupify(str.substring(1));
        return c + ungroupify(str.substring(1));
    }

    //METHOD to reshift characters back to their original.
    public static String decryptString(String str, int shift){

        str = ungroupify(str);
        str = caesarify(str, shift);
        return str;
    }
}


# DaHeck.java

DaHeck is a challange where an input string is "hashed" and then compared to a unicode string. 
If it matches, then the input string is the flag.

However, this "hash" algorithm is on a per-char basis.
It's, therfore, trivial to brute-force each individual character,
using a slightly modified version of the hashing algorithm.

The first thing I did was rewrite the method `check_flag` to not use `char` and instead use `int`.
I decided to do this to simplify the algorithm, and it was probably the wrong choice.
Ints are biggere then chars, and I could have run into some big errors.  
Luckely, the only errors I came accross were overrunning char values.
Char.MAX_VALUE + 1 overlaps to Char.MIN_VALUE, and a quick
`(char)(int)` in some places applied this wrapping.

The modified method is as follows:

```java
    private static boolean rewrite(int[] in){
        int[] daheck = new int[in.length];
        for (int n = 0;;n++) {
            try {

                if (intHeck[n] - in[n % in.length] < 0)
                daheck[n] = (int)(char)(intHeck[n] - in[n % in.length] % 128);
            else
                daheck[n] = (int)(char)(intHeck[n] - in[n % in.length] % 255);

            }catch(Throwable t){
                t.printStackTrace();
                break;
            }

        }

        for(int i = 0;i<unicodeInt.length;i++){
            println(i + ":" + unicodeInt[i] + "," + daheck[i] + ":" + (char)in[i]);
        }

        return Arrays.equals(unicodeInt,daheck);
    }
```
*Including debug printouts*

Now, this can be integrated into a brute force method easily:
```java
    private static String bruteForce(int count) {
        String flag = "";
        for (int index = 0; index < count; index++) {
            for (char c : "abcdefghijklmnopqrstuvwxyz{}0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ~`!@#$%^&*()_-+=|\\{[}]:;\"'<>?,./".toCharArray()) {

                int[] daheck = new int[index + 1];
                int[] in = new int[index + 1];
                in[index] = (int) c;
                for (int n = 0; ; n++) {
                    try {

                        if (intHeck[n] - in[n % in.length] < 0)
                            daheck[n] = (int) (char) (intHeck[n] - in[n % in.length] % 128);
                        else
                            daheck[n] = (int) (char) (intHeck[n] - in[n % in.length] % 255);

                    } catch (Throwable t) {
//                        t.printStackTrace();
                        break;
                    }

                }
                if (unicodeInt[index] == daheck[index]) {
                    flag += c;
                    break;
                }
            }
        }
        return flag;
    }
```
*count represents the amount of characters to try*

This basiclly just runs through characters 0 <= x < count,
and when it finds one that hashes to the correct value, 
it saves it and starts on the next characters.  
`"abcdefghijklmnopqrstuvwxyz{}0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ~`!@#$%^&*()_-+=|\\{[}]:;\"'<>?,./"`
is the character set that I attempt in this code.

Running it is as easy as
```java
public static void main(String ... args) {
    String s = bruteForce(47);
    println(s);
}
```
and I know that the size is 47 because that's the same size as `unicode`




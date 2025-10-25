# EX-NO-11-ELLIPTIC-CURVE-CRYPTOGRAPHY-ECC

## Aim:
To Implement ELLIPTIC CURVE CRYPTOGRAPHY(ECC)


## ALGORITHM:

1. Elliptic Curve Cryptography (ECC) is a public-key cryptography technique based on the algebraic structure of elliptic curves over finite fields.

2. Initialization:
   - Select an elliptic curve equation \( y^2 = x^3 + ax + b \) with parameters \( a \) and \( b \), along with a large prime \( p \) (defining the finite field).
   - Choose a base point \( G \) on the curve, which will be used for generating public keys.

3. Key Generation:
   - Each party selects a private key \( d \) (a random integer).
   - Calculate the public key as \( Q = d \times G \) (using elliptic curve point multiplication).

4. Encryption and Decryption:
   - Encryption: The sender uses the recipient’s public key and the base point \( G \) to encode the message.
   - Decryption: The recipient uses their private key to decode the message and retrieve the original plaintext.

5. Security: ECC’s security relies on the Elliptic Curve Discrete Logarithm Problem (ECDLP), making it highly secure with shorter key lengths compared to traditional methods like RSA.

## Program:
```c
#include <stdio.h>
#include <stdlib.h>

// Elliptic curve parameters for y^2 = x^3 + ax + b over F_p
const int a = 2;
const int b = 3;
const int p = 17;

// Define a structure for a point on the elliptic curve
typedef struct {
    int x;
    int y;
    int is_infinity;
} Point;

// Function to calculate modular inverse using brute force
int mod_inverse(int k, int p) {
    k = (k % p + p) % p;
    for (int x = 1; x < p; x++)
        if ((k * x) % p == 1)
            return x;
    return -1;
}

// Proper modular reduction
int mod(int value, int mod) {
    int result = value % mod;
    return (result < 0) ? result + mod : result;
}

// Point addition on elliptic curve
Point point_addition(Point P, Point Q) {
    Point result;

    if (P.is_infinity) return Q;
    if (Q.is_infinity) return P;

    result.is_infinity = 0;
    int lambda;

    if (P.x == Q.x && P.y == Q.y) {
        // Point Doubling
        int denom = mod_inverse(2 * P.y, p);
        if (denom == -1) {
            result.is_infinity = 1;
            return result;
        }
        lambda = mod((3 * P.x * P.x + a) * denom, p);
    } else {
        // Point Addition
        int denom = mod_inverse(Q.x - P.x, p);
        if (denom == -1) {
            result.is_infinity = 1;
            return result;
        }
        lambda = mod((Q.y - P.y) * denom, p);
    }

    result.x = mod(lambda * lambda - P.x - Q.x, p);
    result.y = mod(lambda * (P.x - result.x) - P.y, p);
    return result;
}

// Scalar Multiplication using Double-and-Add Algorithm
Point scalar_multiplication(Point P, int n) {
    Point result = {0, 0, 1}; // Point at infinity
    Point addend = P;

    while (n > 0) {
        if (n & 1)
            result = point_addition(result, addend);
        addend = point_addition(addend, addend);
        n >>= 1;
    }
    return result;
}

// Main function
int main() {
    Point G = {5, 1, 0};
    int n = 7;

    printf("Base point G: (%d, %d)\n", G.x, G.y);

    Point R = scalar_multiplication(G, n);

    if (R.is_infinity) {
        printf("Result of %d * G: Point at Infinity\n", n);
    } else {
        printf("Result of %d * G: (%d, %d)\n", n, R.x, R.y);
    }

    return 0;
}

```


## Output:

<img width="395" height="222" alt="image" src="https://github.com/user-attachments/assets/354bd65f-0bf0-488f-b4d2-ee57b2f1c3dd" />

## Result:
The ECC program successfully demonstrates point addition and scalar multiplication on an elliptic 
curve over a finite field, and the simulation has been executed and verified successfully.


## 1001-q1 Vectorisation of Code

**Routine1**:  y[i] = alpha * y[i] + beta * z[i]; 

**Routine2**: ``` w[i] = w[i] - beta + alpha * A[i][j] * x[j]; ``` **Correct? [ ]**

## AVX

### Example Function: ConstAdd_AVX
```
unsigned short int ConstAdd_AVX() {
    __m256  ymm1, ymm2, ymm3;
    int i;

    ymm1 = _mm256_set_ps(2.1234f, 2.1234f, 2.1234f, 2.1234f, 2.1234f, 2.1234f, 2.1234f, 2.1234f);
    for (i = 0; i < M; i += 8) {
        ymm2 = _mm256_loadu_ps(&V2[i]);
        ymm3 = _mm256_add_ps(ymm1, ymm2);
        _mm256_storeu_ps(&V1[i], ymm3);
    }
    return 2;
}

```

## AVX Version of Routine1
```
unsigned short int Routine1_AVX() {
    __m256 alpha_vec = _mm256_set1_ps(alpha);
    __m256 beta_vec = _mm256_set1_ps(beta);
    unsigned int i;

    for (i = 0; i < M; i += 8) {
        __m256 y_vec = _mm256_load_ps(&y[i]);
        __m256 z_vec = _mm256_load_ps(&z[i]);
        __m256 result_vec = _mm256_add_ps(_mm256_mul_ps(alpha_vec, y_vec), _mm256_mul_ps(beta_vec, z_vec));
        _mm256_store_ps(&y[i], result_vec);
    }
    return 2;
}
```

## SSE

#### Example Function: ConstAdd_SSE
``` 
unsigned short int ConstAdd_SSE() {
    __m128 num1, num2, num3;
    int i;

    num1 = _mm_set_ps(2.1234f, 2.1234f, 2.1234f, 2.1234f);
    for (i = 0; i < M; i += 4) {
        num2 = _mm_loadu_ps(&V2[i]);
        num3 = _mm_add_ps(num1, num2);
        _mm_storeu_ps(&V1[i], num3);
    }
    return 2;
}

```



## Final comparison Function
```
Compare {
    // Function to Compare Results
    unsigned short int Compare_ConstAdd() {
        int j;

        for (j = 0; j < M; j++) {
            test3[j] = V2[j] + 2.1234f;
        }

        for (j = 0; j < M; j++)
            if (equal(V1[j], test3[j]) == 1) {
                return 1; // Mismatch found
            }

        return 0; // No mismatches
    }

    // Function to Check Equality within Tolerance
    unsigned short int equal(float a, float b) {
        float temp = a - b;
        if ((fabs(temp) / fabs(b)) < EPSILON)
            return 0; // Values are equal
        else
            return 1; // Values are not equal
    }
}


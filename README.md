# Experimental verification of Huffman and Shanon fano code
# Aim:
Consider a discrete memoryless source with symbols and statistics {0.125, 0.0625, 0.25, 0.0625, 0.125, 0.125, 0.25} for its output. 
Apply the Huffman and Shannon-Fano to this source. 
Show that by drawing the tree diagram, and 
Calculate the average code word length, entropy, variance, redundancy, and efficiency.
# Tools Required:
Google Colab
# Theory 

Huffman and Shannon–Fano coding are lossless data compression techniques used to efficiently represent symbols from a discrete memoryless source based on their probabilities. In Shannon–Fano coding, symbols are arranged in descending order and recursively divided into two groups with nearly equal probabilities, assigning binary digits to form prefix codes. Huffman coding, on the other hand, is an optimal bottom-up approach where the least probable symbols are repeatedly combined to form a binary tree, resulting in minimum average codeword length. The performance of these methods is evaluated using entropy, average codeword length, efficiency, redundancy, and variance, with Huffman coding generally providing better efficiency and lower redundancy compared to Shannon–Fano coding.

# Program:
```
Experimental verification of Huffman and Shanon fano code                                                                                                 # Huffman Coding and Shannon-Fano Coding for given source probabilities
# Google Colab Compatible Code

import math
import heapq
from collections import defaultdict
# --------------------------------------------------
# GIVEN SOURCE PROBABILITIES
# --------------------------------------------------
symbols = ['S1', 'S2', 'S3', 'S4', 'S5', 'S6', 'S7']
probabilities = [0.125, 0.0625, 0.25, 0.0625, 0.125, 0.125, 0.25]

print("Symbols and Probabilities")
print("-" * 40)
for s, p in zip(symbols, probabilities):
    print(f"{s} : {p}")
print()
# --------------------------------------------------
# ENTROPY CALCULATION
# --------------------------------------------------

def calculate_entropy(probs):
    H = 0
    for p in probs:
        H += p * math.log2(1 / p)
    return H

entropy = calculate_entropy(probabilities)

print("Entropy Calculation")
print("-" * 40)
print(f"Entropy H(X) = {entropy:.4f} bits/symbol\n")
# --------------------------------------------------
# HUFFMAN CODING
# --------------------------------------------------
class Node:
    def __init__(self, prob, symbol=None, left=None, right=None):
        self.prob = prob
        self.symbol = symbol
        self.left = left
        self.right = right

    def __lt__(self, other):
        return self.prob < other.prob
def generate_huffman_codes(node, current_code="", codes={}):
    if node is None:
        return

    if node.symbol is not None:
        codes[node.symbol] = current_code
        return

    generate_huffman_codes(node.left, current_code + "0", codes)
    generate_huffman_codes(node.right, current_code + "1", codes)

    return codes


def huffman_coding(symbols, probs):
    heap = []

    for s, p in zip(symbols, probs):
        heapq.heappush(heap, Node(p, s))

    while len(heap) > 1:
        left = heapq.heappop(heap)
        right = heapq.heappop(heap)

        merged = Node(left.prob + right.prob, None, left, right)
        heapq.heappush(heap, merged)

    root = heap[0]
    codes = generate_huffman_codes(root)

    return codes, root


huffman_codes, huffman_tree = huffman_coding(symbols, probabilities)

print("Huffman Codes")
print("-" * 40)
for s in symbols:
    print(f"{s} : {huffman_codes[s]}")
print()
# --------------------------------------------------
# SHANNON-FANO CODING
# --------------------------------------------------

def shannon_fano(symbol_prob_list, code_dict={}):
    if len(symbol_prob_list) == 1:
        symbol = symbol_prob_list[0][0]
        return

    total = sum([item[1] for item in symbol_prob_list])

    split_index = 0
    cumulative = 0

    for i in range(len(symbol_prob_list)):
        cumulative += symbol_prob_list[i][1]
        if cumulative >= total / 2:
            split_index = i
            break

    left = symbol_prob_list[:split_index + 1]
    right = symbol_prob_list[split_index + 1:]

    for item in left:
        code_dict[item[0]] += "0"

    for item in right:
        code_dict[item[0]] += "1"

    if len(left) > 1:
        shannon_fano(left, code_dict)

    if len(right) > 1:
        shannon_fano(right, code_dict)

    return code_dict
sorted_pairs = sorted(zip(symbols, probabilities), key=lambda x: x[1], reverse=True)

sf_codes = defaultdict(str)
sf_codes = shannon_fano(sorted_pairs, sf_codes)

print("Shannon-Fano Codes")
print("-" * 40)
for s, _ in sorted_pairs:
    print(f"{s} : {sf_codes[s]}")
print()
# --------------------------------------------------
# PERFORMANCE METRICS
# --------------------------------------------------

def calculate_average_length(codes, probs_dict):
    L = 0
    for s in codes:
        L += probs_dict[s] * len(codes[s])
    return L


def calculate_variance(codes, probs_dict, avg_length):
    variance = 0
    for s in codes:
        variance += probs_dict[s] * ((len(codes[s]) - avg_length) ** 2)
    return variance


prob_dict = dict(zip(symbols, probabilities))

# Huffman Metrics
L_huffman = calculate_average_length(huffman_codes, prob_dict)
Var_huffman = calculate_variance(huffman_codes, prob_dict, L_huffman)
Redundancy_huffman = L_huffman - entropy
Efficiency_huffman = (entropy / L_huffman) * 100

# Shannon-Fano Metrics
L_sf = calculate_average_length(sf_codes, prob_dict)
Var_sf = calculate_variance(sf_codes, prob_dict, L_sf)
Redundancy_sf = L_sf - entropy
Efficiency_sf = (entropy / L_sf) * 100


# --------------------------------------------------
# FINAL RESULTS
# --------------------------------------------------

print("=" * 60)
print("FINAL RESULTS")
print("=" * 60)

print("\nHUFFMAN CODING RESULTS")
print("-" * 40)
print(f"Average Codeword Length = {L_huffman:.4f}")
print(f"Entropy                = {entropy:.4f}")
print(f"Variance               = {Var_huffman:.4f}")
print(f"Redundancy             = {Redundancy_huffman:.4f}")
print(f"Efficiency             = {Efficiency_huffman:.2f}%")

print("\nSHANNON-FANO CODING RESULTS")
print("-" * 40)
print(f"Average Codeword Length = {L_sf:.4f}")
print(f"Entropy                = {entropy:.4f}")
print(f"Variance               = {Var_sf:.4f}")
print(f"Redundancy             = {Redundancy_sf:.4f}")
print(f"Efficiency             = {Efficiency_sf:.2f}%")

print("\nComparison Complete.")
print("=" * 60) 
```
# Calculation:
<img width="900" height="1600" alt="image" src="https://github.com/user-attachments/assets/b23fcc3c-98f9-46d1-9eb8-ae14d3cedfcb" />
<img width="900" height="1600" alt="image" src="https://github.com/user-attachments/assets/7d3ddfa8-eac8-42e5-a9ee-bfe6ec591c75" />

# Output
```

Symbols and Probabilities
----------------------------------------
S1 : 0.125
S2 : 0.0625
S3 : 0.25
S4 : 0.0625
S5 : 0.125
S6 : 0.125
S7 : 0.25

Entropy Calculation
----------------------------------------
Entropy H(X) = 2.6250 bits/symbol

Huffman Codes
----------------------------------------
S1 : 111
S2 : 0110
S3 : 00
S4 : 0111
S5 : 110
S6 : 010
S7 : 10

Shannon-Fano Codes
----------------------------------------
S3 : 00
S7 : 01
S1 : 100
S5 : 101
S6 : 110
S2 : 1110
S4 : 1111

============================================================
FINAL RESULTS
============================================================

HUFFMAN CODING RESULTS
----------------------------------------
Average Codeword Length = 2.6250
Entropy                = 2.6250
Variance               = 0.4844
Redundancy             = 0.0000
Efficiency             = 100.00%

SHANNON-FANO CODING RESULTS
----------------------------------------
Average Codeword Length = 2.6250
Entropy                = 2.6250
Variance               = 0.4844
Redundancy             = 0.0000
Efficiency             = 100.00%

Comparison Complete.
============================================================
``` 
# Results:
The Huffman and Shannon-Fano of the given statistics {0.125, 0.0625, 0.25, 0.0625, 0.125, 0.125, 0.25} using Google Colab are verified.

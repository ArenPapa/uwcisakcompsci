3. Create a program that translate the proteins in the DNA chain as shown below

''',py 

    def dna_to_mrna(dna_sequence):
    
        return dna_sequence.replace('T', 'U')

    def translate_mrna(mrna_sequence):
   
        genetic_code = {
            'UUU': 'F', 'UUC': 'F', 'UUA': 'L', 'UUG': 'L',
            'UCU': 'S', 'UCC': 'S', 'UCA': 'S', 'UCG': 'S',
            'UAU': 'Y', 'UAC': 'Y', 'UAA': '*', 'UAG': '*',
            'UGU': 'C', 'UGC': 'C', 'UGA': '*', 'UGG': 'W',
            'CUU': 'L', 'CUC': 'L', 'CUA': 'L', 'CUG': 'L',
            'CCU': 'P', 'CCC': 'P', 'CCA': 'P', 'CCG': 'P',
            'CAU': 'H', 'CAC': 'H', 'CAA': 'Q', 'CAG': 'Q',
            'CGU': 'R', 'CGC': 'R', 'CGA': 'R', 'CGG': 'R',
            'AUU': 'I', 'AUC': 'I', 'AUA': 'I', 'AUG': 'M',
            'ACU': 'T', 'ACC': 'T', 'ACA': 'T', 'ACG': 'T',
            'AAU': 'N', 'AAC': 'N', 'AAA': 'K', 'AAG': 'K',
            'AGU': 'S', 'AGC': 'S', 'AGA': 'R', 'AGG': 'R',
            'GUU': 'V', 'GUC': 'V', 'GUA': 'V', 'GUG': 'V',
            'GCU': 'A', 'GCC': 'A', 'GCA': 'A', 'GCG': 'A',
            'GAU': 'D', 'GAC': 'D', 'GAA': 'E', 'GAG': 'E',
            'GGU': 'G', 'GGC': 'G', 'GGA': 'G', 'GGG': 'G'
        }
    
   
        protein_sequence = ''
        for i in range(0, len(mrna_sequence), 3):
            codon = mrna_sequence[i:i+3]
            if codon in genetic_code:
                protein_sequence += genetic_code[codon]
            else:
                protein_sequence += '?'
        return protein_sequence

    def translate_dna_to_protein(dna_sequence):
        mrna_sequence = dna_to_mrna(dna_sequence)
        protein_sequence = translate_mrna(mrna_sequence)
        return protein_sequence

    def main():
    
        dna_sequence = input("Enter the DNA sequence: ").upper().strip()
    
   
        if not all(nucleotide in 'ATCG' for nucleotide in dna_sequence):
            print("Invalid DNA sequence. Please enter a sequence containing only A, T, C, and G.")
            return
    

        protein_sequence = translate_dna_to_protein(dna_sequence)
        print(f"Protein Sequence: {protein_sequence}")

    if __name__ == "__main__":
        main()
,

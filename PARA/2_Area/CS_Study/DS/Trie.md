# Trie

문자열 검색을 위한 자료구조

```c
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>
#include <string.h>
//Define size 26
#define ALPHABET_SIZE 26
struct TrieNode {
   struct TrieNode* children[ALPHABET_SIZE];
   bool isEndOfWord;
};
struct Trie {
    struct TrieNode* root;
};
struct TrieNode* createNode() {
   struct TrieNode* node = (struct TrieNode*)malloc(sizeof(struct TrieNode));
   node->isEndOfWord = false;
   for (int i = 0; i < ALPHABET_SIZE; i++) {
      node->children[i] = NULL;
   }
   return node;
}
void insert(struct Trie* trie, const char* key) {
    struct TrieNode* curr = trie->root;
    for (int i = 0; i < strlen(key); i++) {
        int index = key[i] - 'a';
        if (curr->children[index] == NULL) {
            curr->children[index] = createNode();
        }
        curr = curr->children[index];
    }
    curr->isEndOfWord = 1;
}
bool search(struct TrieNode* root, char* word) {
   struct TrieNode* curr = root;
   for (int i = 0; word[i] != '\0'; i++) {
      int index = word[i] - 'a';
      if (curr->children[index] == NULL) {
         return false;
      }
      curr = curr->children[index];
   }
   return (curr != NULL && curr->isEndOfWord);
}
bool startsWith(struct TrieNode* root, char* prefix) {
   struct TrieNode* curr = root;
   for (int i = 0; prefix[i] != '\0'; i++) {
      int index = prefix[i] - 'a';
      if (curr->children[index] == NULL) {
         return false;
      }
      curr = curr->children[index];
   }
   return true;
}
bool deleteWord(struct TrieNode* root, char* word) {
   struct TrieNode* curr = root;
   struct TrieNode* parent = NULL;
   int index;
   for (int i = 0; word[i] != '\0'; i++) {
      index = word[i] - 'a';
      if (curr->children[index] == NULL) {
          return false; // Word does not exist in the Trie
      }
      parent = curr;
      curr = curr->children[index];
   }
   if (!curr->isEndOfWord) {
      return false; // Word does not exist in the Trie
   }
   curr->isEndOfWord = false; // Mark as deleted
   if (parent != NULL) {
      parent->children[index] = NULL; // Remove the child node
   }
   return true;
}
void printWords(struct TrieNode* node, char* prefix) {
    if (node->isEndOfWord) {
        printf("%s\n", prefix);
    }
    for (int i = 0; i < ALPHABET_SIZE; i++) {
        if (node->children[i] != NULL) {
            char* newPrefix = (char*)malloc(strlen(prefix) + 2);
            strcpy(newPrefix, prefix);
            newPrefix[strlen(prefix)] = 'a' + i;
            newPrefix[strlen(prefix) + 1] = '\0';
            printWords(node->children[i], newPrefix);
            free(newPrefix);
        }
    }
}
int main() {
    struct Trie car;
    car.root = createNode();
    insert(&car, "lamborghini");
    insert(&car, "mercedes-Benz");
    insert(&car, "landrover");
    insert(&car, "maruti Suzuki");
   //Before Deletion
   printf("Tries elements before deletion: \n");
   printWords(car.root, "");
   //Deleting the elements
   char* s1  = "lamborghini";
   char* s2 = "landrover";
   printf("Elements to be deleted are: %s and %s", s1, s2);
   deleteWord(car.root, s1);
   deleteWord(car.root, s2);
   //After Deletion
   printf("\nTries elements before deletion: \n");
   printWords(car.root, "");
}
```





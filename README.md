# Anagram-Solver
// Solves anagrams that contain 4 unscrambled words that -when unscrambled- have "key" letters. Then, in the end, there will be a joke. The answer to the joke is the result of the unscrambling of the key letters. One of the arguments needed is the list of words in a dictionary.
#include "BST.h"
//"BST.h" refers to the binary search tree class I created
#include <iostream>
#include <string>
#include <fstream>
#include <boost/algorithm/string.hpp>
#include <algorithm>
using namespace std;

 int main(int argc, char *argv[]){
       ifstream wordList;
       wordList.open("/usr/share/dict/words");
       string word;
       int numLines = 0;
       while (getline(wordList, word) && !wordList.eof()){
               numLines++;
       }
       wordList.close();
       // Warn people about wait time
       cout << "Give it approximately 20 seconds to create the perfectly spread tree. It's worth it" << endl;
       // Open the file

       wordList.open("/usr/share/dict/words");
       string wordsInList[numLines];
       string key;
       int wordIndex = 0;
       bool allLowerCase;
       while (getline(wordList, word) && !wordList.eof()){
               allLowerCase = true;
               for (int i = 0; word[i] != '\0'; i++){
                       if (97 > int(word[i]) || int(word[i]) > 122){
                               allLowerCase = false;
                       }
               }
               if (allLowerCase){
                       wordsInList[wordIndex] = word;
                       wordIndex++;
               }
       }

       BST wordTree;
       int c = 0;
       int length = wordIndex;
       int denominator = 1;
       int index;
       int ind;
       while (c == 0){
               denominator *= 2;
               for (int i = 0; i < (denominator/2); i++){
                       index = length * (1 + (2*i))/denominator;
                       key = wordsInList[index];
                       sort(key.begin(), key.end());
                       wordTree.insert(key, wordsInList[index]);
                       for (int i = index; i < length; i++){
                               wordsInList[i] = wordsInList[i + 1];
                       }
                       length--;
                       if (length == 0){
                               c = 1;
                               break;
                       }
               }
       }
       ifstream jumble;
       jumble.open(argv[1]);

       string jumbleLine;
       string scrambled[4];
       string solved[5];
       string fileLine[6];
       char specLetters[20];
       int r = 0;
       int u = 0;
       int wordLength;
       int fileIndex = 0;
       while (getline(jumble, jumbleLine) && !jumble.eof()){
               fileLine[fileIndex] = jumbleLine;
               wordLength = 0;
               if (fileIndex < 4){
                       for (int j = 0; fileLine[fileIndex][j] != ' '; j++){
                               wordLength++;
                       }
                       scrambled[fileIndex] = fileLine[fileIndex].substr(0, wordLength);
                       sort(scrambled[fileIndex].begin(), scrambled[fileIndex].end());
                       boost::algorithm::to_lower(scrambled[fileIndex]);
                       solved[fileIndex] = wordTree.getValue(scrambled[fileIndex]);
                       cout << solved[fileIndex] << endl;
                       for (int k = wordLength + 1; fileLine[fileIndex][k] != '\0'; k++){
                               if (fileLine[fileIndex][k] == 'O'){
                                       specLetters[r] = solved[fileIndex][k - wordLength - 1];
                                       r++;
                               }
                       }
               }
               if (fileIndex == 4){
                       char spec[r];
                       for (int i = 0; i < r + 1; i++){
                               spec[i] = specLetters[i];
                       }
                       string s = spec;
                       sort(s.begin(), s.end());
                       solved[fileIndex] = wordTree.getValue(s);
                       cout << fileLine[fileIndex] << endl;
               }
               if (fileIndex == 5){
                       for (int i = 0; fileLine[fileIndex][i] != '\0'; i++){
                               if (fileLine[fileIndex][i] != '_'){
                                       cout << fileLine[fileIndex][i];
                               }
                               if (fileLine[fileIndex][i] == '_'){
                                       cout << solved[4][u];
                                       u++;
                               }
                       }
                cout << endl;
                }
                // Go to the next line
                fileIndex++;
        }
}
// The end

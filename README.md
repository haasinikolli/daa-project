#include <stdio.h>
#include <stdlib.h>

#define SIZE 11
#define MAX 2003

// Node for Separate Chaining
typedef struct Node {
    int key;
    struct Node *next;
} Node;

Node *chainTable[SIZE];

// Array for Linear Probing
int linearTable[SIZE];
int status[SIZE];
// status: 0 = Empty, 1 = Occupied, 2 = Deleted

// Hash Function
int hashFunction(int key, int size) {
    int index = key % size;
    return index < 0 ? index + size : index;
}

// Initialize tables
void initialize() {
    for (int i = 0; i < SIZE; i++) {
        chainTable[i] = NULL;
        linearTable[i] = 0;
        status[i] = 0;
    }
}

// ---------------- CHAINING ----------------

// Insert using chaining
void chainInsert(int key) {
    int index = hashFunction(key, SIZE);

    Node *temp = chainTable[index];

    while (temp != NULL) {
        if (temp->key == key) {
            printf("Key already exists!\n");
            return;
        }
        temp = temp->next;
    }

    Node *newNode = (Node *)malloc(sizeof(Node));

    if (newNode == NULL) {
        printf("Memory allocation failed!\n");
        return;
    }

    newNode->key = key;
    newNode->next = chainTable[index];
    chainTable[index] = newNode;

    printf("Key inserted successfully.\n");
}

// Search using chaining
void chainSearch(int key) {
    int index = hashFunction(key, SIZE);
    Node *temp = chainTable[index];

    while (temp != NULL) {
        if (temp->key == key) {
            printf("Key found at index %d.\n", index);
            return;
        }
        temp = temp->next;
    }

    printf("Key not found.\n");
}

// Delete using chaining
void chainDelete(int key) {
    int index = hashFunction(key, SIZE);

    Node *temp = chainTable[index];
    Node *previous = NULL;

    while (temp != NULL) {
        if (temp->key == key) {
            if (previous == NULL)
                chainTable[index] = temp->next;
            else
                previous->next = temp->next;

            free(temp);
            printf("Key deleted successfully.\n");
            return;
        }

        previous = temp;
        temp = temp->next;
    }

    printf("Key not found.\n");
}

// Display chaining table
void chainDisplay() {
    printf("\n--- SEPARATE CHAINING ---\n");

    for (int i = 0; i < SIZE; i++) {
        printf("%d: ", i);

        Node *temp = chainTable[i];

        while (temp != NULL) {
            printf("%d -> ", temp->key);
            temp = temp->next;
        }

        printf("NULL\n");
    }
}

// ---------------- LINEAR PROBING ----------------

// Insert using linear probing
void linearInsert(int key) {
    int index = hashFunction(key, SIZE);
    int firstDeleted = -1;

    for (int i = 0; i < SIZE; i++) {
        int pos = (index + i) % SIZE;

        if (status[pos] == 1 && linearTable[pos] == key) {
            printf("Key already exists!\n");
            return;
        }

        if (status[pos] == 2 && firstDeleted == -1)
            firstDeleted = pos;

        if (status[pos] == 0) {
            int target = (firstDeleted != -1)
                        ? firstDeleted : pos;

            linearTable[target] = key;
            status[target] = 1;

            printf("Key inserted at index %d.\n", target);
            return;
        }
    }

    if (firstDeleted != -1) {
        linearTable[firstDeleted] = key;
        status[firstDeleted] = 1;
        printf("Key inserted at index %d.\n", firstDeleted);
    } else {
        printf("Hash table is full!\n");
    }
}

// Search using linear probing
void linearSearch(int key) {
    int index = hashFunction(key, SIZE);

    for (int i = 0; i < SIZE; i++) {
        int pos = (index + i) % SIZE;

        if (status[pos] == 0) {
            printf("Key not found.\n");
            return;
        }

        if (status[pos] == 1 && linearTable[pos] == key) {
            printf("Key found at index %d.\n", pos);
            return;
        }
    }

    printf("Key not found.\n");
}

// Delete using linear probing
void linearDelete(int key) {
    int index = hashFunction(key, SIZE);

    for (int i = 0; i < SIZE; i++) {
        int pos = (index + i) % SIZE;

        if (status[pos] == 0) {
            printf("Key not found.\n");
            return;
        }

        if (status[pos] == 1 && linearTable[pos] == key) {
            status[pos] = 2; // Tombstone
            printf("Key deleted successfully.\n");
            return;
        }
    }

    printf("Key not found.\n");
}

// Display linear probing table
void linearDisplay() {
    printf("\n--- LINEAR PROBING ---\n");

    for (int i = 0; i < SIZE; i++) {
        printf("%d: ", i);

        if (status[i] == 0)
            printf("EMPTY");
        else if (status[i] == 2)
            printf("DELETED");
        else
            printf("%d", linearTable[i]);

        printf("\n");
    }
}

// Free chaining memory
void freeChain() {
    for (int i = 0; i < SIZE; i++) {
        Node *temp = chainTable[i];

        while (temp != NULL) {
            Node *next = temp->next;
            free(temp);
            temp = next;
        }

        chainTable[i] = NULL;
    }
}

// ---------------- MAIN MENU ----------------

int main() {
    int choice, key;
    initialize();

    while (1) {
        printf("\n================================\n");
        printf("       HASH TABLE PROJECT\n");
        printf("================================\n");
        printf("1. Separate Chaining\n");
        printf("2. Linear Probing\n");
        printf("3. Exit\n");
        printf("Enter your choice: ");

        if (scanf("%d", &choice) != 1)
            break;

        if (choice == 3)
            break;

        if (choice == 1 || choice == 2) {
            int operation;

            printf("\n1. Insert\n");
            printf("2. Search\n");
            printf("3. Delete\n");
            printf("4. Display\n");
            printf("Enter operation: ");

            if (scanf("%d", &operation) != 1)
                break;

            if (operation >= 1 && operation <= 3) {
                printf("Enter key: ");

                if (scanf("%d", &key) != 1)
                    break;
            }

            if (choice == 1) {
                switch (operation) {
                    case 1: chainInsert(key); break;
                    case 2: chainSearch(key); break;
                    case 3: chainDelete(key); break;
                    case 4: chainDisplay(); break;
                    default: printf("Invalid operation!\n");
                }
            } else {
                switch (operation) {
                    case 1: linearInsert(key); break;
                    case 2: linearSearch(key); break;
                    case 3: linearDelete(key); break;
                    case 4: linearDisplay(); break;
                    default: printf("Invalid operation!\n");
                }
            }
        } else {
            printf("Invalid choice!\n");
        }
    }

    freeChain();

    printf("\nProgram terminated successfully.\n");
    return 0;
}

# Data Structure Practical Exam Programs in C
## Simple, Exam-Friendly Programs

**How to use this file**
- Each question is a separate C program. Save only one program at a time in a `.c` file.
- Compile with: `gcc filename.c -o program`
- Run with: `./program` (Linux) or run the executable on Windows.
- The programs use simple variable names and basic logic to make them easier to remember.

---

## 1. Stack Implementation as an ADT Using an Array

**Operations:** Initialize, Push, Pop, Peek, Display; checks overflow and underflow.

```c
#include <stdio.h>
#define MAX 3 

struct Stack{
    int a[MAX];
    int top;
};
void init(struct Stack *s) {
    s->top = -1;
}

void push(struct Stack *s, int value) {
    if (s->top == MAX-1) {
        printf("Stack Overflow\n");
    } else {
        s->a[++s->top] = value;
        printf("%d pushed", value);
    }
}

void pop(struct Stack *s) {
    if (s->top == -1) {
        printf("Stack Underfow");
    } else {
        printf("Popped: %d\n", s->a[s->top--]);
    }
}

void peek(struct Stack *s) {
    if (s->top == -1) {
        printf("Notthing to peek in. Stack is Empty\n");
    } else {
        printf("Top Most Element: %d\n", s->a[s->top]);
    }
}

void display(struct Stack *s) {
    int i;
    if (s->top == -1) {
        printf("Stack is empty to display\n");
        return;
    }
    for (i=s->top; i>=0; i--) {
        printf("%d ", s->a[i]);
    }
    printf("\n");
}

int main() {
    struct Stack s;
    int ch, value;
    init(&s);

    do {
        printf("\n1.Push\n2.Pop\n3.Peek\n4.Display\n5.Exit\n");
        scanf("%d", &ch);
        switch(ch) {
            case 1: 
                printf("Enter value to push: \n");
                scanf("%d", &value);
                push(&s, value);
                break;
            case 2: 
                pop(&s);
                break;
            case 3: 
                peek(&s);
                break;
            case 4: 
                display(&s);
                break;
            case 5: 
                printf("Exiting...");
                break;
            default:
                printf("Invalid Choice");
        }
    } while (ch != 5);
    return 0;
}
```

**Remember:** `top = -1` means empty. Push increases `top`; pop decreases it.

---

## 2. Prefix to Postfix Conversion Using a Stack

**Assumption:** The expression contains single-character operands and operators `+ - * /`, with no spaces. Scan prefix from right to left.

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>
#define MAX 10

typedef struct {
    char arr[MAX][MAX];
    int top;
} Stack;

void init(Stack *s) {
    s->top = -1;
}

int isEmpty(Stack *s) {
    return (s->top == -1);
}

void push(Stack *s, char *str) {
    strcpy(s->arr[++(s->top)], str);
}

char* pop (Stack *s) {
    if(isEmpty(s)) {
        printf("Stack Underflow\n");
        exit(1);
    }
    return s->arr[(s->top)--];
}

int isOperator(char ch) {
    return (ch=='+' || ch=='-' || ch=='*' || ch=='/');
}

int main() {
    Stack s;
    init(&s);
    char prefix[MAX];
    char temp[MAX];

    printf("Enter Prefix Expression: ");
    scanf("%s", prefix);

    int len=strlen(prefix);
    for (int i=len-1; i>=0; i--) {
        if(isalnum(prefix[i])) {
            temp[0] = prefix[i];
            temp[1] = '\0';
            push(&s, temp);
        }
        else if (isOperator(prefix[i])) {
            char op1[MAX], op2[MAX];
            strcpy(op1, pop(&s));
            strcpy(op2, pop(&s));
            strcpy(temp, op1);
            strcat(temp, op2);
            int l = strlen(temp);
            temp[l] = prefix[i];
            temp[l+1] = '\0';
            push(&s, temp);
        }
    }
    printf("Postfix Expression: %s\n", pop(&s));
    return 0;
}
```

**Remember:** Scan right to left. For an operator, pop left then right, join `left + right + operator`.

---

## 3. Parenthesis Matching Using a Stack

**Supports:** `()`, `{}`, `[]`.

```c
#include <stdio.h>
#include <stdlib.h>
#define MAX 20

typedef struct {
    char arr[MAX];
    int top;
} Stack;

void init(Stack *s) {
    s->top = -1;
}

int isEmpty(Stack *s) {
    return (s->top == -1);
}

int isFull(Stack *s) {
    return (s->top == MAX-1);
}

void push(Stack *s, char ch) {
    if(isFull(s)) {
        printf("Stack Overflow");
        exit(1);
    }
    s->arr[++(s->top)] = ch;
}

char pop (Stack *s) {
    if(isEmpty(s)) {
        return '\0';
    }
    return s->arr[(s->top)--];
}

int isMatchingPair(char open, char close) {
    if (open=='(' && close==')') return 1;
    if (open=='[' && close==']') return 1;
    if (open=='{' && close=='}') return 1;
    return 0;
}

int checkBalance(char expr[]) {
    Stack s;
    init(&s);

    for (int i=0; expr[i] != '\0'; i++) {
        char ch = expr[i];

        if (ch=='(' || ch=='[' || ch=='{') {
            push(&s, ch);
        }
        else if (ch==')' || ch==']' || ch=='}') {
            if (isEmpty(&s)) {
                return 0;
            }
            char open = pop(&s);
            if(!isMatchingPair(open, ch)) {
                return 0;
            }
        }
    }
    return isEmpty(&s);
}

int main() {
    char expr[MAX];
    printf("Enter Expression: ");
    scanf("%s", expr);
    if (checkBalance(expr)) {
        printf("Expression is Balanced\n");
    } else {
        printf("Expression is unbalanced\n");
    }
    return 0;
}
```

**Remember:** Push opening brackets; each closing bracket must match the stack top.

---

## 4. Queue Using a Linked List

**Operations:** Enqueue, Dequeue, Display. Nodes are allocated dynamically.

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node {
    int data;
    struct Node* next;
} Node;

typedef struct {
    Node *front;
    Node *rear;
} Queue;

void initialize(Queue *q) {
    q->front = q->rear = NULL;
}

int isEmpty(Queue *q) {
    return (q->front == NULL);
}

void enqueue(Queue *q, int value) {
    Node* newNode = (Node*)malloc(sizeof(Node));
    if (newNode == NULL) {
        printf("Memory allocation failed!\n");
        return;
    }

    newNode->data = value;
    newNode->next = NULL;


    if (isEmpty(q)) {
        q->front = q->rear = newNode;
    } else {
        q->rear->next = newNode;
        q->rear = newNode;
    }

    printf("%d inserted into queue\n", value);
}

void dequeue(Queue *q) {
    if (isEmpty(q)) {
        printf("Queue Underflow! Cannot dequeue\n");
        return;
    }

    Node* temp = q->front;
    printf("Deleted element: %d\n", temp->data);
    q->front = q->front->next;
    if (q->front == NULL) {
        q->rear = NULL;
    }
    free(temp);
}

void display(Queue *q) {
    if (isEmpty(q)) {
        printf("Queue is empty\n");
        return;
    }

    Node* temp = q->front;
    printf("Queue elements: ");
    while (temp != NULL) {
        printf("%d ", temp->data);
        temp = temp->next;
    }
    printf("\n");
}

int main() {
    Queue q;
    initialize(&q);

    int choice, value;

    do {
        printf("\n--- Queue Menu ---\n");
        printf("1. Enqueue\n");
        printf("2. Dequeue\n");
        printf("3. Display\n");
        printf("4. Exit\n");
        printf("Enter your choice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1:
                printf("Enter value to insert: ");
                scanf("%d", &value);
                enqueue(&q, value);
                break;

            case 2:
                dequeue(&q);
                break;

            case 3:
                display(&q);
                break;

            case 4:
                exit(0);

            default:
                printf("Invalid choice\n");
        }
    } while (choice != 4);
    return 0;
}
```

**Remember:** Insert at `rear`, delete from `front`. When the last node is deleted, set both pointers to `NULL`.

---

## 5. Circular Queue Using an Array

```c
#include <stdio.h>
#include <stdlib.h>

#define MAX 5

typedef struct {
    int arr[MAX];
    int front;
    int rear;
} CircularQueue;

void initialize(CircularQueue *q) {
    q->front = -1;
    q->rear = -1;
}

int isEmpty(CircularQueue *q) {
    return (q->front == -1);
}

int isFull(CircularQueue *q) {
    return ((q->rear + 1) % MAX == q->front);
}

void insert(CircularQueue *q, int value) {
    if (isFull(q)) {
        printf("Queue Overflow! Cannot insert %d\n", value);
        return;
    }

    if (isEmpty(q)) {
        q->front = q->rear = 0;
    } else {
        q->rear = (q->rear + 1) % MAX;
    }

    q->arr[q->rear] = value;
    printf("%d inserted\n", value);
}

void delete(CircularQueue *q) {
    if (isEmpty(q)) {
        printf("Queue Underflow! Cannot delete\n");
        return;
    }

    printf("Deleted element: %d\n", q->arr[q->front]);

    if (q->front == q->rear) {
        q->front = q->rear = -1;
    } else {
        q->front = (q->front + 1) % MAX;
    }
}

void display(CircularQueue *q) {
    if (isEmpty(q)) {
        printf("Queue is empty\n");
        return;
    }

    printf("Queue elements: ");
    int i = q->front;

    while (1) {
        printf("%d ", q->arr[i]);
        if (i == q->rear)
            break;
        i = (i + 1) % MAX;
    }
    printf("\n");
}

int main() {
    CircularQueue q;
    initialize(&q);

    int choice, value;

    do {
        printf("\n--- Circular Queue Menu ---\n");
        printf("1. Insert\n");
        printf("2. Delete\n");
        printf("3. Display\n");
        printf("4. Exit\n");
        printf("Enter choice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1:
                printf("Enter value: ");
                scanf("%d", &value);
                insert(&q, value);
                break;

            case 2:
                delete(&q);
                break;

            case 3:
                display(&q);
                break;

            case 4:
                exit(0);

            default:
                printf("Invalid choice\n");
        }
    } while (choice != 4);
    return 0;
}
```

**Remember:** Full condition: `(rear + 1) % SIZE == front`. Use modulo to wrap around.

---

## 6. Printer Job Scheduling System Using a Queue

**Stores:** Job ID and number of pages. This version uses a fixed-size circular queue.

```c
#include <stdio.h>
#include <stdlib.h>

#define MAX 5

typedef struct {
    int jobId;
    int pages;
} PrintJob;

typedef struct {
    PrintJob jobs[MAX];
    int front;
    int rear;
} Queue;

void initialize(Queue *q) {
    q->front = -1;
    q->rear = -1;
}

int isEmpty(Queue *q) {
    return (q->front == -1);
}

int isFull(Queue *q) {
    return (q->rear == MAX - 1);
}

void addJob(Queue *q, int id, int pages) {
    if (isFull(q)) {
        printf("Queue Full! Cannot add new job\n");
        return;
    }

    if (isEmpty(q))
        q->front = 0;

    q->rear++;
    q->jobs[q->rear].jobId = id;
    q->jobs[q->rear].pages = pages;

    printf("Print Job %d added (%d pages)\n", id, pages);
}

void processJob(Queue *q) {
    if (isEmpty(q)) {
        printf("Queue Empty! No jobs to process\n");
        return;
    }

    PrintJob job = q->jobs[q->front];
    printf("Processing Job ID: %d (%d pages)\n", job.jobId, job.pages);

    q->front++;
    if (q->front > q->rear)
        q->front = q->rear = -1;
}

void displayJobs(Queue *q) {
    if (isEmpty(q)) {
        printf("No pending print jobs\n");
        return;
    }

    printf("Pending Print Jobs:\n");
    for (int i = q->front; i <= q->rear; i++) {
        printf("Job ID: %d, Pages: %d\n",
               q->jobs[i].jobId,
               q->jobs[i].pages);
    }
}

int main() {
    Queue q;
    initialize(&q);

    int choice, id, pages;

    do {
        printf("\n--- Printer Job Scheduling ---\n");
        printf("1. Add Print Job\n");
        printf("2. Process Print Job\n");
        printf("3. Display Pending Jobs\n");
        printf("4. Exit\n");
        printf("Enter choice: ");
        scanf("%d", &choice);
        switch (choice) {
            case 1:
                printf("Enter Job ID: ");
                scanf("%d", &id);
                printf("Enter Number of Pages: ");
                scanf("%d", &pages);
                addJob(&q, id, pages);
                break;
            case 2:
                processJob(&q);
                break;
            case 3:
                displayJobs(&q);
                break;
            case 4:
                exit(0);
            default:
                printf("Invalid choice\n");
        }
    } while (choice != 4);
    return 0;
}
```

**Remember:** The first job added is the first job printed (FIFO).

---

## 7. Circular Linked List: Create, Display, Insert at Beginning, Delete from End, Search

```c
#include <stdio.h>
#include <stdlib.h>

struct Node {
    int data;
    struct Node *next;
};

struct Node *last = NULL;

void create() {
    int n, x, i;
    struct Node *p;
    printf("How many nodes? ");
    scanf("%d", &n);

    for (i = 0; i < n; i++) {
        printf("Enter value: ");
        scanf("%d", &x);
        p = (struct Node *)malloc(sizeof(struct Node));
        if (p == NULL) return;
        p->data = x;

        if (last == NULL) {
            last = p;
            p->next = p;
        } else {
            p->next = last->next;
            last->next = p;
            last = p;
        }
    }
}

void display() {
    struct Node *p;
    if (last == NULL) {
        printf("List is empty\n");
        return;
    }
    p = last->next;
    do {
        printf("%d ", p->data);
        p = p->next;
    } while (p != last->next);
    printf("\n");
}

void insertBeginning(int x) {
    struct Node *p = (struct Node *)malloc(sizeof(struct Node));
    if (p == NULL) return;
    p->data = x;

    if (last == NULL) {
        last = p;
        p->next = p;
    } else {
        p->next = last->next;
        last->next = p;
    }
}

void deleteEnd() {
    struct Node *p, *t;
    if (last == NULL) {
        printf("List is empty\n");
        return;
    }
    if (last->next == last) {
        free(last);
        last = NULL;
        return;
    }
    p = last->next;
    while (p->next != last)
        p = p->next;
    t = last;
    p->next = last->next;
    last = p;
    free(t);
}

void search(int x) {
    struct Node *p;
    int pos = 1;
    if (last == NULL) {
        printf("List is empty\n");
        return;
    }
    p = last->next;
    do {
        if (p->data == x) {
            printf("Found at position %d\n", pos);
            return;
        }
        p = p->next;
        pos++;
    } while (p != last->next);
    printf("Not found\n");
}

int main() {
    int ch, x;
    do {
        printf("\n1.Create 2.Display 3.Insert Beginning 4.Delete End 5.Search 6.Exit\n");
        scanf("%d", &ch);
        switch (ch) {
            case 1: create(); break;
            case 2: display(); break;
            case 3: printf("Enter value: "); scanf("%d", &x); insertBeginning(x); break;
            case 4: deleteEnd(); break;
            case 5: printf("Enter value: "); scanf("%d", &x); search(x); break;
            case 6: break;
            default: printf("Invalid choice\n");
        }
    } while (ch != 6);
    return 0;
}
```

**Remember:** `last->next` is the first node. The last node points back to the first node.

---

## 8. Circular Linked List: Create, Display, Insert at End, Delete from Beginning, Count Nodes

```c
#include <stdio.h>
#include <stdlib.h>

struct Node {
    int data;
    struct Node *next;
};

struct Node *last = NULL;

void create() {
    int n, x, i;
    struct Node *p;
    printf("How many nodes? ");
    scanf("%d", &n);
    for (i = 0; i < n; i++) {
        printf("Enter value: ");
        scanf("%d", &x);
        p = (struct Node *)malloc(sizeof(struct Node));
        if (p == NULL) return;
        p->data = x;
        if (last == NULL) {
            last = p;
            p->next = p;
        } else {
            p->next = last->next;
            last->next = p;
            last = p;
        }
    }
}

void display() {
    struct Node *p;
    if (last == NULL) {
        printf("List is empty\n");
        return;
    }
    p = last->next;
    do {
        printf("%d ", p->data);
        p = p->next;
    } while (p != last->next);
    printf("\n");
}

void insertEnd(int x) {
    struct Node *p = (struct Node *)malloc(sizeof(struct Node));
    if (p == NULL) return;
    p->data = x;
    if (last == NULL) {
        last = p;
        p->next = p;
    } else {
        p->next = last->next;
        last->next = p;
        last = p;
    }
}

void deleteBeginning() {
    struct Node *p;
    if (last == NULL) {
        printf("List is empty\n");
        return;
    }
    p = last->next;
    if (p == last)
        last = NULL;
    else
        last->next = p->next;
    free(p);
}

void countNodes() {
    struct Node *p;
    int count = 0;
    if (last == NULL) {
        printf("Nodes: 0\n");
        return;
    }
    p = last->next;
    do {
        count++;
        p = p->next;
    } while (p != last->next);
    printf("Nodes: %d\n", count);
}

int main() {
    int ch, x;
    do {
        printf("\n1.Create 2.Display 3.Insert End 4.Delete Beginning 5.Count 6.Exit\n");
        scanf("%d", &ch);
        switch (ch) {
            case 1: create(); break;
            case 2: display(); break;
            case 3: printf("Enter value: "); scanf("%d", &x); insertEnd(x); break;
            case 4: deleteBeginning(); break;
            case 5: countNodes(); break;
            case 6: break;
            default: printf("Invalid choice\n");
        }
    } while (ch != 6);
    return 0;
}
```

---

## 9. Doubly Linked List: Create, Display, Insert at Position, Delete Beginning, Count Nodes

**Positions start at 1.**

```c
#include <stdio.h>
#include <stdlib.h>

struct Node {
    int data;
    struct Node *prev, *next;
};

struct Node *head = NULL;

void create() {
    int n, x, i;
    struct Node *p, *last = NULL;
    printf("How many nodes? ");
    scanf("%d", &n);
    for (i = 0; i < n; i++) {
        printf("Enter value: ");
        scanf("%d", &x);
        p = (struct Node *)malloc(sizeof(struct Node));
        if (p == NULL) return;
        p->data = x;
        p->next = NULL;
        p->prev = last;
        if (last == NULL)
            head = p;
        else
            last->next = p;
        last = p;
    }
}

void display() {
    struct Node *p = head;
    if (p == NULL) printf("List is empty\n");
    while (p != NULL) {
        printf("%d ", p->data);
        p = p->next;
    }
    printf("\n");
}

void insertPosition(int x, int pos) {
    struct Node *p, *t = head;
    int i;
    if (pos < 1) {
        printf("Invalid position\n");
        return;
    }
    p = (struct Node *)malloc(sizeof(struct Node));
    if (p == NULL) return;
    p->data = x;

    if (pos == 1) {
        p->prev = NULL;
        p->next = head;
        if (head != NULL) head->prev = p;
        head = p;
        return;
    }

    for (i = 1; t != NULL && i < pos - 1; i++)
        t = t->next;
    if (t == NULL) {
        printf("Invalid position\n");
        free(p);
        return;
    }
    p->next = t->next;
    p->prev = t;
    if (t->next != NULL) t->next->prev = p;
    t->next = p;
}

void deleteBeginning() {
    struct Node *t;
    if (head == NULL) {
        printf("List is empty\n");
        return;
    }
    t = head;
    head = head->next;
    if (head != NULL) head->prev = NULL;
    free(t);
}

void countNodes() {
    int count = 0;
    struct Node *p = head;
    while (p != NULL) {
        count++;
        p = p->next;
    }
    printf("Nodes: %d\n", count);
}

int main() {
    int ch, x, pos;
    do {
        printf("\n1.Create 2.Display 3.Insert Position 4.Delete Beginning 5.Count 6.Exit\n");
        scanf("%d", &ch);
        switch (ch) {
            case 1: create(); break;
            case 2: display(); break;
            case 3:
                printf("Enter value and position: ");
                scanf("%d%d", &x, &pos);
                insertPosition(x, pos);
                break;
            case 4: deleteBeginning(); break;
            case 5: countNodes(); break;
            case 6: break;
            default: printf("Invalid choice\n");
        }
    } while (ch != 6);
    return 0;
}
```

---

## 10. Singly Linked List: Create, Display, Insert at Position, Delete by Value

**Positions start at 1.**

```c
#include <stdio.h>
#include <stdlib.h>

struct Node {
    int data;
    struct Node *next;
};

struct Node *head = NULL;

void create() {
    int n, x, i;
    struct Node *p, *last = NULL;
    printf("How many nodes? ");
    scanf("%d", &n);
    for (i = 0; i < n; i++) {
        printf("Enter value: ");
        scanf("%d", &x);
        p = (struct Node *)malloc(sizeof(struct Node));
        if (p == NULL) return;
        p->data = x;
        p->next = NULL;
        if (head == NULL)
            head = p;
        else
            last->next = p;
        last = p;
    }
}

void display() {
    struct Node *p = head;
    if (p == NULL) printf("List is empty\n");
    while (p != NULL) {
        printf("%d ", p->data);
        p = p->next;
    }
    printf("\n");
}

void insertPosition(int x, int pos) {
    struct Node *p, *t = head;
    int i;
    if (pos < 1) {
        printf("Invalid position\n");
        return;
    }
    p = (struct Node *)malloc(sizeof(struct Node));
    if (p == NULL) return;
    p->data = x;

    if (pos == 1) {
        p->next = head;
        head = p;
        return;
    }
    for (i = 1; t != NULL && i < pos - 1; i++)
        t = t->next;
    if (t == NULL) {
        printf("Invalid position\n");
        free(p);
        return;
    }
    p->next = t->next;
    t->next = p;
}

void deleteValue(int x) {
    struct Node *t = head, *prev = NULL;
    while (t != NULL && t->data != x) {
        prev = t;
        t = t->next;
    }
    if (t == NULL) {
        printf("Value not found\n");
        return;
    }
    if (prev == NULL)
        head = t->next;
    else
        prev->next = t->next;
    free(t);
    printf("Deleted\n");
}

int main() {
    int ch, x, pos;
    do {
        printf("\n1.Create 2.Display 3.Insert Position 4.Delete by Value 5.Exit\n");
        scanf("%d", &ch);
        switch (ch) {
            case 1: create(); break;
            case 2: display(); break;
            case 3:
                printf("Enter value and position: ");
                scanf("%d%d", &x, &pos);
                insertPosition(x, pos);
                break;
            case 4: printf("Enter value to delete: "); scanf("%d", &x); deleteValue(x); break;
            case 5: break;
            default: printf("Invalid choice\n");
        }
    } while (ch != 5);
    return 0;
}
```

---

## 11. Doubly Linked List: Create, Display, Insert Beginning, Delete End, Search

```c
#include <stdio.h>
#include <stdlib.h>

struct Node {
    int data;
    struct Node *prev, *next;
};

struct Node *head = NULL;

void create() {
    int n, x, i;
    struct Node *p, *last = NULL;
    printf("How many nodes? ");
    scanf("%d", &n);
    for (i = 0; i < n; i++) {
        printf("Enter value: ");
        scanf("%d", &x);
        p = (struct Node *)malloc(sizeof(struct Node));
        if (p == NULL) return;
        p->data = x;
        p->next = NULL;
        p->prev = last;
        if (last == NULL) head = p;
        else last->next = p;
        last = p;
    }
}

void display() {
    struct Node *p = head;
    if (p == NULL) printf("List is empty\n");
    while (p != NULL) {
        printf("%d ", p->data);
        p = p->next;
    }
    printf("\n");
}

void insertBeginning(int x) {
    struct Node *p = (struct Node *)malloc(sizeof(struct Node));
    if (p == NULL) return;
    p->data = x;
    p->prev = NULL;
    p->next = head;
    if (head != NULL) head->prev = p;
    head = p;
}

void deleteEnd() {
    struct Node *p = head;
    if (head == NULL) {
        printf("List is empty\n");
        return;
    }
    if (head->next == NULL) {
        free(head);
        head = NULL;
        return;
    }
    while (p->next != NULL)
        p = p->next;
    p->prev->next = NULL;
    free(p);
}

void search(int x) {
    struct Node *p = head;
    int pos = 1;
    while (p != NULL) {
        if (p->data == x) {
            printf("Found at position %d\n", pos);
            return;
        }
        p = p->next;
        pos++;
    }
    printf("Not found\n");
}

int main() {
    int ch, x;
    do {
        printf("\n1.Create 2.Display 3.Insert Beginning 4.Delete End 5.Search 6.Exit\n");
        scanf("%d", &ch);
        switch (ch) {
            case 1: create(); break;
            case 2: display(); break;
            case 3: printf("Enter value: "); scanf("%d", &x); insertBeginning(x); break;
            case 4: deleteEnd(); break;
            case 5: printf("Enter value: "); scanf("%d", &x); search(x); break;
            case 6: break;
            default: printf("Invalid choice\n");
        }
    } while (ch != 6);
    return 0;
}
```

---

## BST 

```c
#include <stdio.h>
#include <stdlib.h>

typedef struct Node {
    int data;
    struct Node *left;
    struct Node *right;
} Node;
Node *root = NULL;

Node *createNode (int value) {
    Node *newNode = (Node*)malloc(sizeof(Node));
    newNode->data = value;
    newNode->left = newNode->right = NULL;
    return newNode;
}

Node *insert(Node *root, int value) {
    if (root == NULL) {
        return createNode(value);
    }
    if (value < root->data) {
        root->left = insert(root->left, value);
    }
    else if (value > root->data) {
        root->right = insert(root->right, value);
    }
    return root;
}

Node *findMin(Node *root) {
    while (root && root->left != NULL) {
        root = root->left;
    }
    return root;
}

Node *deleteNode(Node *root, int key) {
    if (key < root->data)
        root->left = deleteNode(root->left, key);
    else if (key > root->data)
        root->right = deleteNode(root->right, key);
    else {
        if (root->left == NULL || root->right == NULL) {
            Node *temp = root->left ? root->left : root->right;
            free(root);
            return temp;
        }
        Node *temp = findMin(root->right);
        root->data = temp->data;
        root->right = deleteNode(root->right, temp->data);
    }
    return root;
}

Node *findMax(Node *root) {
    while (root && root->right != NULL) {
        root = root->right;
    }
    return root;
}

int search(Node *root, int key) {
    if (root == NULL) {
        return 0;
    }
    if (key == root->data) {
        return 1;
    } else if (key < root->data) {
        return search(root->left, key);
    } else {
        return search(root->right, key);
    }
}

void inorder(Node *root) {
    if (root != NULL) {
        inorder(root->left);
        printf("%d ", root->data);
        inorder(root->right);
    }
}

void preorder(Node *root) {
    if (root != NULL) {
        printf("%d ", root->data);
        preorder(root->left);
        preorder(root->right);
    }
}

void postorder(Node *root) {
    if (root != NULL) {
        postorder(root->left);
        postorder(root->right);
        printf("%d ", root->data);
    }
}

int main() {
    int choice, value;
    Node *temp;
    
    do {
        printf("\n\n-- Binary Search Tree Menu --");
        printf("\n1.Insert\n2.Delete\n3.Search\n4.Inorder\n5.Preorder\n6.Postorder\n7.Find_Min\n8.Find_Max\n9.Exit");
        printf("\nEnter your choice: ");
        scanf("%d", &choice);

        switch (choice) {
            case 1: 
                printf("Enter value to insert: ");
                scanf("%d", &value);
                root = insert(root, value);
                printf("Element Inserted");
                break;
            case 2:
                printf("Enter the value to delete: ");
                scanf("%d", &value);
                if (search(root, value)) {
                    root = deleteNode(root, value);
                    printf("Element deleted successfully");
                } else {
                    printf("Element not found");
                }
                break;
            case 3:
                printf("Enter value to search: ");
                scanf("%d", &value);
                if (search(root, value)) {
                    printf("Element found");
                } else {
                    printf("Element not found");
                }
                break;
            case 4: 
                printf("Inorder: ");
                inorder(root);
                break;
            case 5: 
                printf("Preorder: ");
                preorder(root);
                break;
            case 6: 
                printf("Postorder: ");
                postorder(root);
                break;
            case 7: 
                temp = findMin(root);
                if (temp != NULL) {
                    printf("Minimum Element: %d", temp->data);
                } else {
                    printf("Tree is empty");
                }
                break;
            case 8: 
                temp = findMax(root);
                if (temp != NULL) {
                    printf("Maximum element: %d", temp->data);
                } else {
                    printf("Tree is empty");
                }
                break;
            case 9: 
                printf("Exiting...");
                break;
            default:
                printf("Invalid Choice");
        }
    } while (choice != 9);
    return 0;
}
```


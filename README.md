#include <stdio.h>
#include <stdlib.h>

struct Student
{
    int rollNo;
    char name[50];
    float marks[3];
    float total;
};

void addStudent()
{
    FILE *fp;
    struct Student s;

    fp = fopen("students.dat", "ab");

    if (fp == NULL)
    {
        printf("File cannot be opened!\n");
        return;
    }

    printf("Enter Roll Number: ");
    scanf("%d", &s.rollNo);

    printf("Enter Name: ");
    scanf(" %[^\n]", s.name);

    printf("Enter marks of 3 subjects:\n");
    for (int i = 0; i < 3; i++)
    {
        printf("Subject %d: ", i + 1);
        scanf("%f", &s.marks[i]);
    }

    s.total = s.marks[0] + s.marks[1] + s.marks[2];

    fwrite(&s, sizeof(s), 1, fp);
    fclose(fp);

    printf("Student record added successfully!\n");
}

void displayStudents()
{
    FILE *fp;
    struct Student s;

    fp = fopen("students.dat", "rb");

    if (fp == NULL)
    {
        printf("No records found!\n");
        return;
    }

    printf("\n----- Student Records -----\n");

    while (fread(&s, sizeof(s), 1, fp))
    {
        printf("\nRoll Number: %d\n", s.rollNo);
        printf("Name: %s\n", s.name);
        printf("Marks: %.2f %.2f %.2f\n",
               s.marks[0], s.marks[1], s.marks[2]);
        printf("Total Marks: %.2f\n", s.total);
    }

    fclose(fp);
}

void generateRankList()
{
    FILE *fp;
    struct Student students[100], temp;
    int count = 0;

    fp = fopen("students.dat", "rb");

    if (fp == NULL)
    {
        printf("No records found!\n");
        return;
    }

    while (fread(&students[count],
                 sizeof(struct Student),
                 1, fp))
    {
        count++;
    }

    fclose(fp);

    // Sorting in descending order of total marks
    for (int i = 0; i < count - 1; i++)
    {
        for (int j = i + 1; j < count; j++)
        {
            if (students[i].total < students[j].total)
            {
                temp = students[i];
                students[i] = students[j];
                students[j] = temp;
            }
        }
    }

    printf("\n----- Rank List -----\n");
    printf("Rank\tRoll No\tName\t\tTotal\n");

    for (int i = 0; i < count; i++)
    {
        printf("%d\t%d\t%s\t\t%.2f\n",
               i + 1,
               students[i].rollNo,
               students[i].name,
               students[i].total);
    }
}

int main()
{
    int choice;

    do
    {
        printf("\n===== Student Record Management =====\n");
        printf("1. Add Student Record\n");
        printf("2. Display All Student Records\n");
        printf("3. Generate Rank List\n");
        printf("4. Exit\n");

        printf("Enter your choice: ");
        scanf("%d", &choice);

        switch (choice)
        {
        case 1:
            addStudent();
            break;

        case 2:
            displayStudents();
            break;

        case 3:
            generateRankList();
            break;

        case 4:
            printf("Exiting Program...\n");
            break;

        default:
            printf("Invalid Choice!\n");
        }

    } while (choice != 4);

    return 0;
}

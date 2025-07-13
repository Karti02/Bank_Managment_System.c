#include<stdio.h>
#include<string.h>

void createAccount();
void depositCash();
void withdrawCash();
void checkBalance();   
void accessAllAccounts(); 
const char* filename = "account.dat";

typedef struct {
    int accountNumber;
    char name[50];
    float balance;
}Account;

int main() {

    while (1) {
        int option;
        printf("\n      ***WELCOME to THE Bank Management System ***\n");
        printf("           __________________________________\n\n");
        printf("1. Create an Account:\n");
        printf("2. Deposit Money:\n");
        printf("3. Withdraw Money:\n");
        printf("4. Check Balance:\n");
        printf("5. Exit the System:\n");
        printf("6.Access all Accounts(Only For the Manager):\n");
        printf("Enter your choice:\n ");
        scanf("%d", &option);

        switch (option) {
            case 1:
                createAccount();
                break;
            case 2:
                depositCash();
                break;
            case 3:
                withdrawCash();
                break;  
            case 4:
                checkBalance();
                break;
            case 5:
                printf("\nExiting the system...\nTHANK YOU FOR YOUR VISIT!\n");
                return 0;
            case 6:
                accessAllAccounts();
                break;
            default:
                printf("Invalid option! Please try again.\n");
                break;
        }
                                  
    }
    return 0;
}

void createAccount() {

    printf("\nCreating a new account...\n");
    Account newAccount;

    FILE *file = fopen("account.dat", "ab+"); 
    if (file == NULL) {
        printf("Error opening file!\n");
        return;
    }
    printf("Enter account number: ");
    scanf("%d", &newAccount.accountNumber);
    getchar(); 
    printf("Enter account holder's name: ");
    fgets(newAccount.name, sizeof(newAccount.name), stdin);
    newAccount.name[strcspn(newAccount.name, "\n")] = 0;
    newAccount.balance = 0;
    printf("Account created successfully!\n");

    fwrite(&newAccount, sizeof(Account), 1, file);
    fclose(file);
    printf("CONGRATS!! Your New Account Is Created Successfully!\n");
    
}
void depositCash() {
    FILE *file = fopen("account.dat", "rb+");
    if (file == NULL) {
        printf("Error opening file!\n");
        return;
    }

    printf("\nEnter account number to deposit money: ");
    int accountNumber;
    float money;
    Account readAccount;
    scanf("%d", &accountNumber);
    getchar();
    printf("Enter amount to deposit: ");
    scanf("%f", &money);
    getchar();

    while(fread(&readAccount, sizeof(readAccount), 1, file)) {
        if (readAccount.accountNumber == accountNumber) {
            readAccount.balance += money;
            fseek(file, -sizeof(Account), SEEK_CUR);
            fwrite(&readAccount, sizeof(readAccount), 1, file);
            fclose(file);
            printf("Money deposited successfully!\n");
            printf("Your new balance is: %.2f\n", readAccount.balance);
            return;
        }
    }
    fclose(file);
    printf("Account not found!\n");
    printf("Please check your account number and try again.\n");
}
void withdrawCash() {
    FILE *file = fopen("account.dat", "rb+");
    if (file == NULL) {
        printf("Error opening file!\n");
        return;
    }
    int accountNumber;
    float money;
    Account depositAccount;
    printf("\nEnter account number to withdraw money: ");
    scanf("%d", &accountNumber);
    getchar();
    printf("Enter amount you want to withdraw: ");
    scanf("%f", &money);
    getchar();
    while(fread(&depositAccount, sizeof(Account), 1, file)) {
        if (depositAccount.accountNumber == accountNumber) {
            if (depositAccount.balance >= money) {  
                depositAccount.balance -= money;
                fseek(file, -sizeof(depositAccount), SEEK_CUR);
                fwrite(&depositAccount, sizeof(depositAccount), 1, file);
                printf("Money withdrawn successfully!\n");
                printf("Your new balance is: %.2f\n", depositAccount.balance);
                }else {
                printf("Insufficient balance!\n", depositAccount.balance);
            }
            fclose(file);
            return;
        }
    }
    fclose(file);
    printf("Account not found!\n");
    printf("Please check your account number and try again.\n");
}
void checkBalance() {
    FILE *file = fopen("account.dat", "rb");
    if (file == NULL) {
        printf("Error opening file!\n");
        return;
    }
    int accountNumber;
    Account readAccount;
    printf("Enter account number: ");
    scanf("%d", &accountNumber);
    getchar(); 
    while(fread(&readAccount, sizeof(Account), 1, file)) {
        if (readAccount.accountNumber == accountNumber) {
            printf("Your Current Balance is %.2f\n", readAccount.balance);
            fclose(file);
            return;
        }
    }
    fclose(file);
    printf("Account not found!\n");
    printf("Please check your account number and try again.\n");
}
void accessAllAccounts() {
    FILE *file = fopen("account.dat", "rb");
    if (file == NULL) {
        printf("Error opening file!\n");
        return;
    }
    Account readAccount;
    printf("\nENTER YOUR PASSWORD TO ACCESS ALL ACCOUNTS: ");
    char password[20];
    scanf("%s", password);
    if (strcmp(password, "KARTI*123") != 0) {
        printf("Incorrect password! Access denied.\n");
        fclose(file);
        return;
    }
    printf("\nAll Accounts:\n");
    printf("Account Number\t\tBalance\n");
    printf("--------------------------------------------------\n");
    while(fread(&readAccount, sizeof(Account), 1, file)) {
        printf("%d\t\t\t%.2f\n", readAccount.accountNumber, readAccount.balance);
    }
    fclose(file);
    printf("End of account list.\n");
}   

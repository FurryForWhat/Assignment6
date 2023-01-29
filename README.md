//
// Created by ADMIN on 18-Jan-23.
//  pre 1 struct
#include "stdio.h"
#include "stdlib.h"
#define SIZE 2

int emailFound= -1;         //user input နဲ့ database ကဟာနဲ့တူမတူ တိုက်စစ်ဖို့ Flag
int passwordFound= -1;
int gloablIndex= 0;
char userLoginEmail[30];
int emailFormat= -1;

int charCounting(char toCount[50]);
void printingAllData();
void login();
void myStrCmp(char userInputCharacterl[50]);
void checkPassword(char inputPass[50], int flagIndex);
void recordAllDataToFile();
void userActionSector();
void loadingAllDataFromFile();
void Registration();
void lobby();
void regi_again();

void checkEmailFormat(char inputEmail[50]);

struct  Info{
    int id;
    int age;
    char name[30];
    char email[20];
    char password[30];

};

struct Info worker[SIZE]; //အလုပ်သမား 3 ရဲ့ infoတွေသိမ်းတယ်

void inputLoop();

int main(){

    inputLoop();
    login();
    lobby();
//        loadingAllDataFromFile();
//        printingAllData();
    return 0;
}

void inputLoop(){

    for (int i = 0; i < SIZE; i++) {
        printf("For User %d\n", i+1);

        worker[i].id= i;

        printf("Enter age:");
        scanf("%d",&worker[i].age);

        printf("Enter name:");
        scanf(" %[^\n]",&worker[i].name);

        printf("Enter email:");
        scanf(" %[^\n]",&worker[i].email);
        checkEmailFormat(worker[i].email);
        if(emailFormat == 1){
            printf("Email format isn't correct, Try again!!!!\n");
            printf("Enter email:");
            scanf(" %[^\n]",&worker[i].email);
            checkEmailFormat(worker[i].email);
        }


        printf("Enter password:");
        scanf(" %[^\n]",&worker[i].password);
    }
}
void lobby(){
    int lob=0;
    printf("This is Lobby section!!\n");
    printf("Press 1 for Register:\n");
    printf("Press 2 for Login:\n");
    printf("Press 3 for Exit:\n");

    scanf("%d",&lob);

    if(lob == 1){

        Registration();
    } else if( lob == 2){
        login();
    } else if(lob == 3){
        recordAllDataToFile();
        exit(1);
    } else{
        printf("Invalid Option:\n");
        lobby();
    }
}

void printingAllData(){
    for (int i = 0; i < SIZE; ++i) {
        printf("id: %d -name: %s - age: %d - email: %s - password: %s\n",worker[i].id,worker[i].name, worker[i].age, worker[i].email, worker[i].password);
    }
}

void login(){
    char loginEmail[30];        //user inputကိုလက်ခံတာ
    char loginPassword[30];

    printf("This is login form:\n");

    printf("Enter your valid Email to login: ");
    scanf(" %[^\n]",&loginEmail);

    emailFound= -1;
    myStrCmp(loginEmail);
    checkEmailFormat(loginEmail);

    printf("Enter password:");
    scanf(" %[^\n]", &loginPassword);
    passwordFound= -1;
    checkPassword(loginPassword, emailFound);

    if(emailFound != -1 && passwordFound == 1){
         userActionSector();
    } else{
       if(emailFound == -1){
           printf("Invalid email:\n");
           login();    //မရပြန်မေး
       } else{
           printf("Invalid Password:\n");
           login();
       }
    }

}

void myStrCmp( char userInputCharacter[50]){     // Strcmp build in function ကိုမသုံးချင်လို့ ဆောက်ပြီးသုံးတယ် အပေါ်မှာက
    int sameCount= 0;                                                         // charကို အချင်းချင်း == နဲ့တိုက်စစ်တာ မရလို့

    int second= charCounting(userInputCharacter); //charကို လက်ခံပြီးတော့ တိုက်စစ်မဲ့ဟာ user inputကဟာနဲ့

    for (int i = 0; i < SIZE; ++i) {
        int first= charCounting(worker[i].email);           //charကို လက်ခံပြီးတော့ တိုက်စစ်မဲ့ဟာ struct ထဲကဟာနဲ့
        if(first == second){

            for (int j = 0; j < first; ++j) {

                if(worker[i].email[j] != userInputCharacter[j]){
                    break;
                } else{
                    sameCount++;            // sameCountက တိုတယ် တူတာကို စုထားတာ
                }
            }
        }
        if(second == sameCount){
            emailFound= i;
            break;
        }
    }

}

int charCounting( char toCount[50]){   //characterCounting to the end untill \0
    int charCount= 0;
    for (int i = 0; i < 50; ++i) {

        if(toCount[i] == '\0'){
            break;
        }else{
            charCount++;
        }
    }
    return charCount;
}

void checkEmailFormat(char inputEmail[50]){
    int at=-1;
    int dot= -1;
    int space= -1;
    for (int i = 0; i < charCounting(inputEmail) ; ++i) {
        if(inputEmail[i] == '@'){
            at = i;
        } else if(inputEmail[i] == '.') {
            dot= i;
        } else if(inputEmail[i] == ' '){
            space= i;
        }
    }
    if( at != -1 && dot != -1 && space == -1){
        printf("");
    } else{
        printf("Email format is incorrect\n");
        emailFormat= 1;

    }

}

void checkPassword(char inputPass[50], int flagIndex){
    int sameCount=0;
    int passCountLogin= charCounting(inputPass);
    int passCountDb= charCounting(worker[emailFound].password);

   if(passCountDb == passCountLogin){
       for (int i = 0; i < passCountLogin; ++i) {
           if(inputPass[i] == worker[emailFound].password[i]){
               sameCount++;
           } else{
               break;
           }
       }
       if(sameCount == passCountLogin){
        passwordFound= 1;
       }
   }

}

void recordAllDataToFile(){
    FILE  *fptr= fopen("structDb.txt","w");

    if(fptr == NULL){
        printf("Error at recordingAllDataToFile :\n");
    } else{
        for (int i = 0; i < SIZE; ++i) {
            fprintf(fptr, "%d    %d %s %s %s%c",worker[i].id,worker[i].age, worker[i].name, worker[i].email, worker[i].password,'\n');
        }
        printf("Recording all data to structDb.txt completed:\n");
    }
    fclose(fptr);

}

void userActionSector(){
    int userAction=0;
    printf("Welcome Sir: %s\n",worker[emailFound].name);
    printf("Press 1 to User Action Sector:\n");
    printf("Press 2 to Home:\n");
    printf("Press 3 to Exit:\n");
    scanf("%d",&userAction);

    if(userAction == 1){
        printf("This is user action sector:\n");
    } else if(userAction == 2){
        login();
    } else if(userAction == 3){
    recordAllDataToFile();
    } else{
        printf("Invalid Option");
        userActionSector();
    }
}

void loadingAllDataFromFile(){
    FILE *fptr= fopen("structDb.txt","r");

    if(fptr == NULL){
        printf("Error at Loading:\n");
    } else{
        for (int i = 0; i < SIZE; ++i) {
            fscanf(fptr,"%d%d%s%s%s",&worker[i].id, &worker[i].age,&worker[i].name, &worker[i].email, &worker[i].password);
        if(worker[i].name[0]== '\0'){
            break;
        }
        gloablIndex++;

        }
    }

}

void Registration(){
    char inputEmail[30];
    int count=0;
    printf("This is registration!\n");
    printf("Enter your email:");
    scanf(" %[^\n]", &inputEmail);
    checkEmailFormat(inputEmail);

    if(emailFormat == 1){
        printf("Invalid email format, try again!!!!\n");
        printf("Enter your email:");
        scanf(" %[^\n]", &inputEmail);

        checkEmailFormat(inputEmail);
    }



    emailFound=-1;
    myStrCmp(inputEmail);

    if(emailFound == -1){
        printf("U can register enter your name ph pss");
        regi_again();

    } else{
        printf("Your email is in the list.Try with new one!!\n");
        Registration();
    }
}

void regi_again(){
    printf("Here you can register\n");
}

#include<iostream> //to Include library so we can use cout and cin
#include<fstream>
#include<cctype>//tp use is digit in built function
using namespace std;
//functions prototype
bool validCNIC();
void checkin();
void checkout();
void specificDetailsfromRoom();
void specificDetailsfromCNIC();
void diplayCurrentBookings();
void history();

//making structure functionto store all details
struct details{
int age;
string CNIC;
int room_no,floor_no;
string name;
int rent;
int days;
bool booking=true;//true for empty room

};

//Global Variables
string Tname;
string Tcnic;
int Tage;
int Tdays;
int rent;
int floorNo,roomNo;


details arr[3][15];

// void UpdateTotalfloorAndroom(){

// cout<<"Enter Total number of room in each floor : ";
// cin>>T_room;
// cout<<"Enter Total Number of room in Hotel : ";
// cin>>T_floor;
// cout<<endl;




// }
bool validCNIC(string cnic){

if (cnic.length()!=13){
    return false;
}

for(char c:cnic){//range based for loop (datatype variable : container) take each charcter and check
    if(!isdigit(c)){
        return false;
    }
}

return true;

}

//functions for checkin
void checkin(){

cout << "Enter  Your Name :";
cin >> Tname;
cout<<endl;

cout<<"Enter CNIC : ";
cin>>Tcnic; 
while(!validCNIC(Tcnic)){
    cout<<"Enter valid CNIC format of 13 digits (35103xxxxxx55)";
    cin>>Tcnic;
    }
cout<<endl;

cout << "Enter Your Age :";
cin >> Tage;
cout<<endl;

cout << "Enter No of Days You Want To Stay :";
cin >> Tdays;
cout<<endl;

cout<<"Enter Floor No you want(Enter 0 for ground floor): "<<endl;
cin>>floorNo;
cout<<endl;

cout<<"Enter Room No you want: ";
cin>>roomNo;
cout<<endl;
//to check if the room is already booked or not
while(arr[floorNo][roomNo-1].booking == false){
    cout<<"Room is Already booked.Its not Available"<<endl;

    cout<<"Enter new Floor No you want(Enter 0 for ground floor): "<<endl;
    cin>>floorNo;

    cout<<"Enter new Room No you want: "<<endl;
    cin>>roomNo;
}



if(floorNo==0){
    rent=1000;
}else if(floorNo==1){
        rent=2000;
}else{
             rent=3000;
}

arr[floorNo][roomNo-1].name=Tname;

arr[floorNo][roomNo-1].CNIC=Tcnic;

arr[floorNo][roomNo-1].room_no=roomNo;

arr[floorNo][roomNo-1].floor_no=floorNo;


arr[floorNo][roomNo-1].days=Tdays;

arr[floorNo][roomNo-1].rent=Tdays*rent;

arr[floorNo][roomNo-1].booking=false;

cout<<"Your total rent is "<<rent*Tdays<<endl;

cout<<"You successfully checked In"<<endl;

ofstream HistoryFile;

HistoryFile.open("ChecksHistory.txt",ios::app);

HistoryFile << "===CHECK IN====\n";
HistoryFile << "Name: "<<arr[floorNo][roomNo-1].name<<endl;
HistoryFile << "CNIC: " << arr[floorNo][roomNo - 1].CNIC << endl;
HistoryFile << "ROOM No: " << arr[floorNo][roomNo - 1].room_no << endl;
HistoryFile << "Floor No: " << arr[floorNo][roomNo - 1].floor_no << endl;
HistoryFile << "Staying for " << arr[floorNo][roomNo - 1].days << " days" << endl;
HistoryFile << "Total Rent: " << arr[floorNo][roomNo - 1].rent << endl;
HistoryFile << "------------------------------------------- \n"; 

HistoryFile.close();

}

//checking out from room
void checkout(){
string name,cnic;

cout<<"Enter Your name : ";
cin>>name;

cout<<"Enter CNIC\n 13 digits (35103xxxxxx55) : ";
cin>>cnic;
while(!validCNIC(cnic)){
    cout<<"Enter valid CNIC format of 13 digits (35103xxxxxx55)";
    cin>>cnic;
}


bool check=true;

for(int floor=0;floor<3;floor++){

    for(int room=0;room<15;room++){

        if(arr[floor][room].CNIC==cnic){

            cout<<endl;
            cout<<"Name : "<<arr[floor][room].name<<endl;
            cout<<"Room : "<<arr[floor][room].room_no<<"|| Floor No. "<<arr[floor][room].floor_no<<endl;
            cout<<"Checked Out"<<endl;


            ofstream HistoryFile;

            HistoryFile.open("ChecksHistory.txt",ios::app);

            HistoryFile << "===CHECK OUT====\n";
            HistoryFile << "Name: "<<arr[floor][room].name<<endl;
            HistoryFile << "CNIC: "<<arr[floor][room].CNIC<<endl;
            HistoryFile << "ROOM No: " << arr[floor][room].room_no << endl;
            HistoryFile << "Floor No: " << arr[floor][room].floor_no << endl;
            HistoryFile << "Staying for " << arr[floor][room].days << " days" << endl;
            HistoryFile << "Total Rent: " << arr[floor][room].rent << endl;
            HistoryFile << "-------------------------------" << endl;

            HistoryFile.close();

            check=false;
            arr[floor][room].CNIC="";
            arr[floor][room].room_no=-1;
            arr[floor][room].floor_no=-1;
            arr[floor][room].name="null";
            arr[floor][room].days=-1;
            arr[floor][room].rent=-1;
            arr[floor][room].booking=true;

            }
        
        }
    }if(check=true){//condition if cnic is wrong
            cout<<"\n There is no room booked with this CNIC \n";
        }

}
//to find details of person using its room and floor no.
void specificDetailsfromRoom(){

int roomNo,floorNO;

cout<<"Enter room no to find its details";
cin>>roomNo;

cout<<"Enter floor no to find its details";
cin>>floorNo;

cout<<"====DEATILS OF ROOM NO: " << roomNo << " and FLOOR NO: "<<floorNo<<" =====\n";
if(arr[floorNo][roomNo-1].booking==false){

    cout<<"Name : "<<arr[floorNo][roomNo-1].name<<endl;

    cout<<"CNIC : "<<arr[floorNo][roomNo-1].CNIC<<endl;

    cout<<"Days :"<<arr[floorNo][roomNo-1].days<<endl;

    cout<<"Total Rent : "<<arr[floorNo][roomNo-1].rent<<endl;

    }else{
    cout<<" Room is Empty ";
    }

}

//to find details of person using its CNIC
void  specificDetailsfromCNIC(){

string cnic;
cout<<"Enter CNIC: ";
cin>>cnic;
while(!validCNIC(cnic)){
    cout<<"Enter valid CNIC format of 13 digits (35103xxxxxx55)";
    cin>>cnic;
}
bool check=true;//if no one is present with this cnic in room

for(int floor=0;floor<3;floor++){

    for(int room=0;room<15;room++){
        if(arr[floor][room].CNIC==cnic){

            cout<<"Name: "<<arr[floor][room].name<<endl;

            cout<<"ROOM No: "<<arr[floor][room].room_no<<endl;

            cout<<"Flooor No: "<<arr[floor][room].floor_no<<endl;

            cout<<"Staying for "<<arr[floor][room].days<<" days\n";

            cout<<"Total Rent: "<<arr[floor][room].rent<<endl;

            check=false;
            }
        
        }
    }
if(check==true){
        cout<<"No one with this cnic is present in Hotel";
    }
}



void displayCurrentBookings(){
cout<<endl<<"=====Current Bookings In Hotels Now=====";
    for(int floor=0;floor<3;floor++){

    for(int room=0;room<15;room++){
        if(arr[floor][room].booking==false){

            cout<<endl;

            cout<<"Name: "<<arr[floor][room].name<<endl;

            cout<<"ROOM No: "<<arr[floor][room].room_no<<endl;

            cout<<"Flooor No: "<<arr[floor][room].floor_no<<endl;

            cout<<"Staying for "<<arr[floor][room].days<<" days\n";

            cout<<"Total Rent: "<<arr[floor][room].rent<<endl;

            cout<<endl;
            }
        
        }
    }


}

void history(){

// fstream fout;
// fout.open("CurrentBooking.txt");
// for(int n=0;n<3;n++){

//     for(int j=0;j<15;j++){

//         if(arr[n][j].booking == false){
//             cout<<"Room No : "<<j<<" Floor No : "<<n<<endl;
//             cout<<"Name: "<<arr[n][j].name<<endl;
//             cout<<"Age: "<<arr[n][j].age<<endl;
//             cout<<"Days: "<<arr[n][j].days<<endl;
//             cout<<arr[floorNo][roomNo-1].rent<<endl;
//         }

//     }
//  }








// fout.close();


}


int main(){

///intialiizng all bookings with true
for(int n=0;n<3;n++){

    for(int j=0;j<15;j++){
        arr[n][j].booking=true;
    }
 }


int choice;

while(choice!=0){

    
    cout<<"Enter choice: ";
    cin>>choice;

switch(choice){

case 1:
        displayCurrentBookings();
        break;
case 2:
    checkin();
    break;
case 3:
 specificDetailsfromRoom();
    break;
 case 4:
 specificDetailsfromCNIC();
 break;

 case 5:
 checkout();
}
    }
        }

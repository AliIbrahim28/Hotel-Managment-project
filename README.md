#include<iostream> //to Include library so we can use cout and cin
#include<fstream>
using namespace std;
//functions prototype
void checkin();
void checkout();
void specificDetailsfromRoom();
void specificDetailsfromCNIC();
void diplayCurrentBookings();
void history();

//making structure functionto store all details
struct details{
int age;
int CNIC;
int room_no,floor_no;
string name;
int rent;
int days;
bool booking=true;//true for empty room

};

//Global Variables
string Tname;
int Tcnic;
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

//functions for checkin
void checkin(){

cout<<"Enter CNIC : ";
cin>>Tcnic; 
cout<<endl;

cout << "Enter  Your Name :";
cin >> Tname;
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

arr[floorNo][roomNo-1].CNIC=Tcnic;


arr[floorNo][roomNo-1].room_no=roomNo;

arr[floorNo][roomNo-1].floor_no=floorNo;

arr[floorNo][roomNo-1].name=Tname;

arr[floorNo][roomNo-1].days=Tdays;

arr[floorNo][roomNo-1].rent=Tdays*rent;

arr[floorNo][roomNo-1].booking=false;

cout<<"Your total rent is "<<rent*Tdays<<endl;

cout<<"You successfully checked In"<<endl;

}

#include<iostream>
#include<fstream>
#include<string>
#include<cctype>
#include<ctime>
using namespace std;

/*
    ===== ADMIN FUNCTION PROTOTYPES =====
*/
bool adminLogin();
void adminRegister();
void changeAdminPassword();

/*
    ===== HOTEL FUNCTION PROTOTYPES =====
*/
bool validCNIC(string);
void showAvailableRooms();
void checkin();
void checkout();
void specificDetailsfromRoom();
void specificDetailsfromCNIC();
void displayCurrentBookings(int floor = 0, int room = 0);
void readandstore();
void totalRevenueReport();  // New feature

/*
    ===== STRUCT TO STORE ROOM DETAILS =====
*/
struct details {
    int age;
    int room_no;
    int floor_no;
    int rent;
    int days;
    string CNIC;
    string name;
    string checkinTime;   // new variable to store check-in time
    bool booking = true;  // true means room is empty
};

/*
    ===== HISTORY FUNCTION PROTOTYPES =====
*/
void storeCheckinHistory(details &d);
void storeCheckoutHistory(details &d);
void displayHistory();

/*
    ===== GLOBAL VARIABLES =====
*/
string Tname;
string Tcnic;
int Tdays;
int rent;
int floorNo;
int roomNo;
long long totalRevenue = 0;  // Changed to long long to prevent overflow

// Hotel has 3 floors and 15 rooms per floor
details arr[3][15];

/*
    ================= ADMIN FUNCTIONS =================
*/

// First time admin registration
void adminRegister() {
    string user;
    string pass;

    cout << "First Time Admin Registration\n";

    cout << "Enter Username : ";
    getline(cin, user);

    cout << "Enter Password : ";
    getline(cin, pass);

    ofstream out("admin.txt");
    if(!out) {
        cout << "Error creating admin.txt\n";
        return;
    }

    out << user << endl;
    out << pass << endl;
    out.close();

    cout << "Admin Registered Successfully\n";
}

// Admin login function
bool adminLogin() {
    ifstream in("admin.txt");
    string storedUser;
    string storedPass;

    if(!in || !getline(in, storedUser) || !getline(in, storedPass)) {
        in.close();
        adminRegister();
        return true;
    }

    in.close();

    string user;
    string pass;

    cout << "Enter Admin Username : ";
    getline(cin, user);

    cout << "Enter Admin Password : ";
    getline(cin, pass);

    if(user == storedUser && pass == storedPass) {
        cout << "Login Successful\n";
        return true;
    } else {
        cout << "Wrong Username or Password\n";
        return false;
    }
}

// Change admin username & password
void changeAdminPassword() {
    ifstream in("admin.txt");
    if(!in) {
        cout << "No Admin Found\n";
        return;
    }

    string storedUser;
    string storedPass;
    getline(in, storedUser);
    getline(in, storedPass);
    in.close();

    string oldUser;
    string oldPass;
    cin.ignore();
    cout << "Enter Old Username : ";
    getline(cin, oldUser);
    cout << "Enter Old Password : ";
    getline(cin, oldPass);

    if(oldUser == storedUser && oldPass == storedPass) {
        string newUser;
        string newPass;
        cout << "Enter New Username : ";
        getline(cin, newUser);
        cout << "Enter New Password : ";
        getline(cin, newPass);

        ofstream out("admin.txt", ios::trunc);
        if(!out) {
            cout << "Error opening admin.txt\n";
            return;
        }

        out << newUser << endl;
        out << newPass << endl;
        out.close();

        cout << "Password Updated Successfully\n";
    } else {
        cout << "Old Credentials Incorrect\n";
    }
}

/*
    ================= HOTEL FUNCTIONS =================
*/

// CNIC validation (13 digits only)
bool validCNIC(string cnic) {
    if(cnic.length() != 13) {
        return false;
    }

    for(char c : cnic) {
        if(!isdigit(c)) {
            return false;
        }
    }

    return true;
}

// Room check-in function with improved error handling
void checkin() {
    cin.ignore();

    cout << "Enter Your Name : ";
    getline(cin, Tname);

    cout << "Enter CNIC : ";
    cin >> Tcnic;

    while(!validCNIC(Tcnic)) {
        cout << "Enter valid CNIC : ";
        cin >> Tcnic;
    }

    // Check if CNIC already has a booking
    for(int f = 0; f < 3; f++) {
        for(int r = 0; r < 15; r++) {
            if(arr[f][r].CNIC == Tcnic && !arr[f][r].booking) {
                cout << "This CNIC already has a booking!\n";
                return;
            }
        }
    }

    cout << "Enter No of Days You Want To Stay : ";
    while(!(cin >> Tdays) || Tdays <= 0) {
        cin.clear();
        cin.ignore(1000, '\n');
        cout << "Enter a valid number of days: ";
    }

    cout << "Select Floor Type:\n";
    cout << "0 - ECONOMIC      (Per day rent: 1000)\n";
    cout << "1 - ECONOMIC++    (Per day rent: 2000)\n";
    cout << "2 - BUSINESS      (Per day rent: 3000)\n";

    cout << "Enter Floor No : ";
    while(!(cin >> floorNo) || floorNo < 0 || floorNo > 2) {
        cin.clear();
        cin.ignore(1000, '\n');
        cout << "Invalid input. Enter Floor No (0-2) : ";
    }

    cout << "Enter Room No : ";
    while(!(cin >> roomNo) || roomNo < 1 || roomNo > 15) {
        cin.clear();
        cin.ignore(1000, '\n');
        cout << "Invalid Room No (1-15). Enter again: ";
    }

    // Check if room is already booked
    while(arr[floorNo][roomNo-1].booking == false) {
        cout << "Room Already Booked\n";

        cout << "Select Floor Type:\n";
        cout << "0 - ECONOMIC      (Per day rent: 1000)\n";
        cout << "1 - ECONOMIC++    (Per day rent: 2000)\n";
        cout << "2 - BUSINESS      (Per day rent: 3000)\n";

        cout << "Enter Floor No : ";
        while(!(cin >> floorNo) || floorNo < 0 || floorNo > 2) {
            cin.clear();
            cin.ignore(1000, '\n');
            cout << "Invalid input. Enter Floor No (0-2) : ";
        }

        cout << "Enter Room No : ";
        while(!(cin >> roomNo) || roomNo < 1 || roomNo > 15) {
            cin.clear();
            cin.ignore(1000, '\n');
            cout << "Invalid Room No (1-15). Enter again: ";
        }
    }

    // Rent calculation
    if(floorNo == 0) {
        rent = 1000;
    } else if(floorNo == 1) {
        rent = 2000;
    } else {
        rent = 3000;
    }

    time_t now = time(0);
    char* dt = ctime(&now);

    arr[floorNo][roomNo-1].name = Tname;
    arr[floorNo][roomNo-1].CNIC = Tcnic;
    arr[floorNo][roomNo-1].room_no = roomNo;
    arr[floorNo][roomNo-1].floor_no = floorNo;
    arr[floorNo][roomNo-1].days = Tdays;
    arr[floorNo][roomNo-1].rent = rent * Tdays;
    arr[floorNo][roomNo-1].booking = false;
    arr[floorNo][roomNo-1].checkinTime = dt;

    totalRevenue += arr[floorNo][roomNo-1].rent;

    cout << "Total Rent : " << rent * Tdays << endl;
    cout << "Checked In Successfully at " << dt;

    storeCheckinHistory(arr[floorNo][roomNo-1]);
}

// Room checkout
void checkout() {
    string cnic;
    bool found = false;

    cout << "Enter CNIC : ";
    cin >> cnic;

    for(int floor = 0; floor < 3; floor++) {
        for(int room = 0; room < 15; room++) {
            if(arr[floor][room].CNIC == cnic) {
                storeCheckoutHistory(arr[floor][room]);

                arr[floor][room].booking = true;
                arr[floor][room].CNIC = "";
                arr[floor][room].name = "";
                arr[floor][room].days = 0;
                arr[floor][room].rent = 0;
                arr[floor][room].checkinTime = "";

                cout << "Checked Out Successfully\n";
                found = true;
            }
        }
    }

    if(!found) {
        cout << "No Booking Found\n";
    }
}

// Show available rooms
void showAvailableRooms() {
    bool anyAvailable = false;

    cout << "\n===== AVAILABLE ROOMS =====\n";

    for(int floor = 0; floor < 3; floor++) {
        if(floor == 0) {
            cout << "\nFloor 0 (ECONOMIC):\n";
        } else if(floor == 1) {
            cout << "\nFloor 1 (ECONOMIC++):\n";
        } else {
            cout << "\nFloor 2 (BUSINESS):\n";
        }

        for(int room = 0; room < 15; room++) {
            if(arr[floor][room].booking) {
                cout << "Room No: " << room + 1 << " is Available\n";
                anyAvailable = true;
            }
        }
    }

    if(!anyAvailable) {
        cout << "\nNo rooms available right now.\n";
    }
}


// Find details using room & floor
void specificDetailsfromRoom(){

    int room, floor;

    cout<<"Enter Room No : ";
    cin>>room;

    cout<<"Enter Floor No : ";
    cin>>floor;

    if(arr[floor][room-1].booking == false){
        cout<<"Name : "<<arr[floor][room-1].name<<endl;
        cout<<"CNIC : "<<arr[floor][room-1].CNIC<<endl;
        cout<<"Days : "<<arr[floor][room-1].days<<endl;
        cout<<"Rent : "<<arr[floor][room-1].rent<<endl;
        cout<<"Check-in Time : "<<arr[floor][room-1].checkinTime<<endl; // display check-in time
    }
    else{
        cout<<"Room is Empty\n";
    }
}

// Find details using CNIC
void specificDetailsfromCNIC(){

    string cnic;
    bool found = false;

    cout<<"Enter CNIC : ";
    cin>>cnic;

    for(int floor = 0; floor < 3; floor++){
        for(int room = 0; room < 15; room++){

            if(arr[floor][room].CNIC == cnic){
                cout<<"Name : "<<arr[floor][room].name<<endl;
                cout<<"Room No : "<<arr[floor][room].room_no<<endl;
                cout<<"Floor No : "<<arr[floor][room].floor_no<<endl;
                cout<<"Check-in Time : "<<arr[floor][room].checkinTime<<endl; // show check-in time
                found = true;
            }
        }
    }

    if(!found){
        cout<<"No Record Found\n";
    }
}

// Display all current bookings

void displayCurrentBookings(int floor, int room) {

    if(floor >= 3) return;  // Base case: all floors processed


    if(arr[floor][room].booking == false) {
    if(floor==0){
            cout<<"======ECONOMIC=====\n";
        }else if(floor==1){
            cout<<"======ECONOMIC++=====\n";
        }else{
            cout<<"======BUISNESS=====\n";
        }
        cout<<"Floor : "<<arr[floor][room].floor_no<<endl;
        cout<<"Name : "<<arr[floor][room].name<<endl;
        cout<<"Room : "<<arr[floor][room].room_no<<endl;
        cout<<"Rent : "<<arr[floor][room].rent<<endl;
        cout<<"Check-in Time : "<<arr[floor][room].checkinTime<<endl;
        cout<<endl;
    }

    // Move to next room
    if(room < 14) 
        displayCurrentBookings(floor, room + 1);
    else 
        displayCurrentBookings(floor + 1, 0);
}

// Read stored bookings from file
void readandstore(){

    fstream checkin;
    checkin.open("array.txt", ios::in);

    if(!checkin) return;

    int floor, room;

    while(checkin>>floor>>room){

        checkin.ignore();
        getline(checkin, arr[floor][room].name);
        getline(checkin, arr[floor][room].CNIC);
        checkin>>arr[floor][room].room_no;
        checkin>>arr[floor][room].floor_no;
        checkin>>arr[floor][room].rent;
        checkin>>arr[floor][room].days;
        checkin.ignore();
        getline(checkin, arr[floor][room].checkinTime); // read check-in time
        arr[floor][room].booking = false;


    }

    checkin.close();
}

/*
    ================= HISTORY FUNCTIONS =================
*/

// Append check-in to history file
void storeCheckinHistory(details &d) {
    ofstream hist("history.txt", ios::app); // append mode
    hist << "CHECK-IN\n";
    hist << "Name: " << d.name << "\n";
    hist << "CNIC: " << d.CNIC << "\n";
    hist << "Room No: " << d.room_no << "\n";
    hist << "Floor No: " << d.floor_no << "\n";
    hist << "Days: " << d.days << "\n";
    hist << "Rent: " << d.rent << "\n";
    hist << "Check-in Time: " << d.checkinTime << "\n";
    hist << "------------------------\n";
    hist.close();
}

// Append checkout to history file
void storeCheckoutHistory(details &d) {
    ofstream hist("history.txt", ios::app); // append mode
    hist << "CHECK-OUT\n";
    hist << "Name: " << d.name << "\n";
    hist << "CNIC: " << d.CNIC << "\n";
    hist << "Room No: " << d.room_no << "\n";
    hist << "Floor No: " << d.floor_no << "\n";
    hist << "Rent Paid: " << d.rent << "\n";
    hist << "------------------------\n";
    hist.close();
}

// Display all history
void displayHistory() {
    ifstream hist("history.txt");
    if(!hist){
        cout << "No history found\n";
        return;
    }

    string line;
    while(getline(hist, line)){
        cout << line << endl;
    }

    hist.close();
}

/*
    ================= TOTAL REVENUE REPORT =================
*/
void totalRevenueReport() {
    cout << "===== TOTAL REVENUE REPORT =====" << endl;
    cout << "Total Revenue Till Now: " << totalRevenue << " PKR" << endl;
}

/*
    ================= MAIN FUNCTION =================
*/
int main(){

    // Admin authentication
    if(!adminLogin()){
        return 0;
    }

    // Initialize all rooms as empty
    for(int i = 0; i < 3; i++){
        for(int j = 0; j < 15; j++){
            arr[i][j].booking = true;
        }
    }

    // Load previous bookings
    readandstore();

    int choice;

    do{
        cout << "\n========== HOTEL MANAGEMENT MENU ==========\n";
        cout << "1. Check-In\n";
        cout << "2. Check-Out\n";
        cout << "3. Display Current Bookings\n";
        cout << "4. Search Details by Room\n";
        cout << "5. Search Details by CNIC\n";
        cout << "6. Display History\n";
        cout << "7. Change Admin Password\n";
        cout << "8. Total Revenue Report\n";
        cout << "9. Display Available Rooms\n";
        cout << "0. Exit\n";
        cout << "==========================================\n";
        cout << "Enter choice : ";
        cin >> choice;

        switch(choice){

            case 1:
                checkin();
                break;

            case 2:
                checkout();
                break;

            case 3:
                displayCurrentBookings();
                break;

            case 4:
                specificDetailsfromRoom();
                break;

            case 5:
                specificDetailsfromCNIC();
                break;

            case 6:
                displayHistory();
                break;

            case 7:
                changeAdminPassword();
                break;

            case 8:
                totalRevenueReport();
                break;

            case 9:
                showAvailableRooms();
                break;

            case 0:
                cout << "Exiting Program...\n";
                break;

            default:
                cout << "Invalid Choice! Try Again.\n";
        }

    }while(choice != 0);

    // Save current bookings to file
    fstream fout;
    fout.open("array.txt", ios::out | ios::trunc);

    for(int floor = 0; floor < 3; floor++){
        for(int room = 0; room < 15; room++){

            if(arr[floor][room].booking == false){

                fout << floor << endl;
                fout << room << endl;
                fout << arr[floor][room].name << endl;
                fout << arr[floor][room].CNIC << endl;
                fout << arr[floor][room].room_no << endl;
                fout << arr[floor][room].floor_no << endl;
                fout << arr[floor][room].rent << endl;
                fout << arr[floor][room].days << endl;
                fout << arr[floor][room].checkinTime << endl;
            }
        }
    }

    fout.close();
    return 0;
}

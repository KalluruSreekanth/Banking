# Banking
The code uploaded on the repo is for the DTS harman batch of  training c++



#include<iostream>
#include<fstream>
#include<cctype>
#include<iomanip>
using namespace std;


class account
{
		int acno;
		char name[50];
		int deposit;
		int type;
	public:
		void create_account();	//function to get data from user
		void show_account() const;	//function to show data on screen
		void modify();		//function to add new data
		void dep(int);		//function to accept amount and add to balance amount
		void draw(int);	//function to accept amount and subtract from balance amount
		void report() const;	//function to show data in tabular format
		int retacno() const;	//function to return account number
		int retdeposit() const;	//function to return balance amount
		char rettype() const;	//function to return type of account
};         //class ends here

void account::create_account()
{

	cout<<"\nEnter The account No. :";
	cin>>acno;

	cout<<"\nEnter The Name of The account Holder : ";
	cin.ignore();
	
	cin.getline(name,50);
	cout<<"\nEnter Type of The account:\n1.SAVINGS\n2.CURRENT\n";
	cin>>type; 
	
	cout<<"\nEnter The Initial amount: ";
	cin>>deposit;
	
	cout<<"\n\nAccount Created Succesfully........";
}

void account::show_account() const
{
	cout<<"\nAccount Number      : "<<acno;
	
	cout<<"\nAccount Holder Name : ";
	
	cout<<name;

	if(type==1)
		cout<<"\nType of Account     : SAVINGS Account";
	else
		cout<<"\nType of Account     : CURRENT Account";
	
	cout<<"\nBalance amount : "<<deposit;
}


void account::modify()
{
	cout<<"\nAccount Number             : "<<acno;
	
	cout<<"\nModify Account Holder Name : ";
	cin.ignore();
	cin.getline(name,50);

	cout<<"\nModify Type of The account :\n1.SAVINGS\n2.CURRENT\n";
	cin>>type; 
	
	cout<<"\nModify Balance amount      : ";
	cin>>deposit;
}

	
void account::dep(int x)
{
	deposit+=x;
	retdeposit();
}
	
void account::draw(int x)
{
	cout<<"Insufficienct Balance in your account";
	retdeposit();
}
	
void account::report() const
{
	cout<<acno<<setw(10)<<" "<<name<<setw(10)<<" "<<type<<setw(6)<<deposit<<endl;
}

	
int account::retacno() const
{
	return acno;
}

int account::retdeposit() const
{
	return deposit;
}

char account::rettype() const
{
	return type;
}


void write_account();	//function to write record in binary file
void display_sp(int);	//function to display account details given by user
void modify_account(int);	//function to modify record of file
void delete_account(int);	//function to delete record of file
void display_all();			//function to display all account details
void deposit_withdraw(int, int); // function to desposit/withdraw amount for given account

int main()
{
	char ch;
	int num;
	cout<<"\n\tBANK MANAGEMENT SYSTEM\n";
	do
	{
		
		cout<<"\n\tMAIN MENU";
		cout<<"\n\t1. NEW ACCOUNT";
		cout<<"\n\t2. DEPOSIT AMOUNT";
		cout<<"\n\t3. WITHDRAW AMOUNT";
		cout<<"\n\t4. BALANCE ENQUIRY";
		cout<<"\n\t5. ALL ACCOUNT HOLDER LIST";
		cout<<"\n\t6. CLOSE AN ACCOUNT";
		cout<<"\n\t7. MODIFY AN ACCOUNT";
		cout<<"\n\t8. EXIT";
		cout<<"\n\tPlease...Enter your choice: ";
		cin>>ch;
		
		
		switch(ch)
		{
			case '1':write_account();
						break;
			case '2':cout<<"\nEnter The account No. : "; cin>>num;
						deposit_withdraw(num, 1);
						break;
			case '3':cout<<"\nEnter The account No. : "; cin>>num;
						deposit_withdraw(num, 2);
						break;
			case '4':cout<<"\nEnter The account No. : "; cin>>num;
						display_sp(num);
						break;
			case '5':display_all();
						break;
			case '6':cout<<"\nEnter The account No. : "; cin>>num;
						delete_account(num);
						break;
			case '7':cout<<"\n\tEnter The account No. : "; cin>>num;
						modify_account(num);
						break;
			case '8':cout<<"\n\tThanks for using bank managemnt system...";
						break;
			
			default :cout<<"\n\tINVALID OPTION.......";
		}
		
		cin.ignore();
		cin.get();

	}while(ch!='8');
	
	return 0;
}


//    	function to write in file


void write_account()
{
	account ac;
	ofstream outFile;
	outFile.open("account.dat",ios::binary|ios::app);
	ac.create_account();
	outFile.write(reinterpret_cast<char *> (&ac), sizeof(account));
	outFile.close();
}

//function to read specific record from file

void display_sp(int n)
{
	account ac;
	bool flag=false;
	ifstream inFile;
	inFile.open("account.dat",ios::binary);
	if(!inFile)
	{
		cout<<"File could not be open !! Press any Key...";
		return;
	}
	cout<<"\nBALANCE DETAILS\n";

    	while(inFile.read(reinterpret_cast<char *> (&ac), sizeof(account)))
	{
		if(ac.retacno()==n)
		{
			ac.show_account();
			flag=true;
		}
	}
	inFile.close();
	if(flag==false)
		cout<<"\n\nAccount number does not exist";
}

//    	function to modify record of file

void modify_account(int n)
{
	bool found=false;
	account ac;
	fstream File;
	File.open("account.dat",ios::binary|ios::in|ios::out);
	if(!File)
	{
		cout<<"File could not be open !! Press any Key...";
		return;
	}
	while(!File.eof() && found==false)
	{
		File.read(reinterpret_cast<char *> (&ac), sizeof(account));
		if(ac.retacno()==n)
		{
			ac.show_account();
			cout<<"\n\nEnter The New Details of account"<<endl;
			ac.modify();
			int pos=(-1)*static_cast<int>(sizeof(account));
			File.seekp(pos,ios::cur);
			File.write(reinterpret_cast<char *> (&ac), sizeof(account));
			cout<<"\n\n\t Record Updated";
			found=true;
		  }
	}
	File.close();
	if(found==false)
		cout<<"\n\n Record Not Found ";
}

//    	function to delete record of file


void delete_account(int n)
{
	account ac;
	ifstream inFile;
	ofstream outFile;
	inFile.open("account.dat",ios::binary);
	if(!inFile)
	{
		cout<<"File could not be open !! Press any Key....";
		return;
	}
	outFile.open("Temp.dat",ios::binary);
	inFile.seekg(0,ios::beg);
	while(inFile.read(reinterpret_cast<char *> (&ac), sizeof(account)))
	{
		if(ac.retacno()!=n)
		{
			outFile.write(reinterpret_cast<char *> (&ac), sizeof(account));
		}
	}
	inFile.close();
	outFile.close();
	remove("account.dat");
	rename("Temp.dat","account.dat");
	cout<<"\n\n\tRecord Deleted ..";
}

//    	function to display all accounts deposit list.

void display_all()
{
	account ac;
	ifstream inFile;
	inFile.open("account.dat",ios::binary);
	if(!inFile)
	{
		cout<<"File could not be open !! Press any Key...";
		return;
	}
	cout<<"\n\n\t\tACCOUNT HOLDER LIST\n\n";
	cout<<"====================================================\n";
	cout<<"A/c no.      NAME           Type  Balance\n";
	cout<<"====================================================\n";
	while(inFile.read(reinterpret_cast<char *> (&ac), sizeof(account)))
	{
		ac.report();
	}
	inFile.close();
}

//    	function to deposit and withdraw amounts

void deposit_withdraw(int n, int option)
{
	int amt;
	bool found=false;
	account ac;
	fstream File;
	File.open("account.dat", ios::binary|ios::in|ios::out);
	if(!File)
	{
		cout<<"File could not be open !! Press any Key...";
		return;
	}
	while(!File.eof() && found==false)
	{
		File.read(reinterpret_cast<char *> (&ac), sizeof(account));
		if(ac.retacno()==n)
		{
			ac.show_account();
			if(option==1)
			{
				cout<<"\n\tTO DEPOSITE AMOUNT ";
				cout<<"\nEnter The amount to be deposited : ";
				cin>>amt;
				ac.dep(amt);
			}
			if(option==2)
			{
				cout<<"\n\tTO WITHDRAW AMOUNT ";
				cout<<"\nEnter The amount to be withdraw : ";
				cin>>amt;
				int bal=ac.retdeposit()-amt;
				if((bal<500 && ac.rettype()==1) || (bal<1000 && ac.rettype()==2))
					cout<<"Insufficient balance";
				else
					ac.draw(amt);
			}
			int pos=(-1)*static_cast<int>(sizeof(ac));
			File.seekp(pos,ios::cur);
			File.write(reinterpret_cast<char *> (&ac), sizeof(account));
			cout<<"\n\n\t Record Updated";
			found=true;
	       }
         }
	File.close();
	if(found==false)
		cout<<"\n\n Record Not Found ";
}

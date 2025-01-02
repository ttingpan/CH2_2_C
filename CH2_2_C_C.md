# [4번 과제] CH 2 C++ Summary
## 도전 기능
기존 도서관 관리 프로그램에 필수 기능을 추가한 상태에서 아래 대여 기능을 추가해주세요

- 특정 책의 대여 여부를 알 수 있어야 합니다.
- 책의 이름으로 대여 여부를 검색하고 대여가 아닌 경우 대여할 수 있어야 합니다.
- 책의 작가로 대여 여부를 검색하고 대여가 아닌 경우 대여할 수 있어야 합니다.
- 책을 반납할 수 있어야 합니다.
- 모든 책의 재고는 난이도를 낮추기 위해 3권으로 통일 합니다.

도전 기능 구현까지 완료한 후 전체적인 구조를 그리면 아래와 같습니다.
어디까지나 참조일 뿐 꼭 이대로 구현할 필요는 없습니다.

![image](https://github.com/user-attachments/assets/7c83b3a1-b989-4533-a603-536fcc18c357)

## 평가 기준
	
- 책 대여 여부를 정확히 조회할 수 있는지 확인
- 책 제목 및 작가로 대여 시 재고 감소가 올바르게 반영되는지 확인
- 반납 기능이 재고를 정상적으로 복구하는지 확인
- 객체지향적 설계와 책임 분리의 원칙을 준수했는지
- 재고 관리와 대여/반납 로직이 논리적이고 일관성 있는지
- 대여 및 반납 기능에서 에러 처리 및 사용자 친화적 메시지 출력 여부
- 재고 관리를 효율적으로 구현했는지(중복 코드 최소화).
- 프로그램 확장 가능성을 고려했는지(예: 다른 재고 정책 추가 가능성)

```c#
#include <iostream>
#include <vector>
#include <string>
#include <unordered_map>

using namespace std; // namespace std 사용

class Book {
public:
    string title;
    string author;

    Book(const string& title, const string& author)
        : title(title), author(author) {
    }
};

class BookManager {
private:
    vector<Book> books; // 책 목록 저장

public:
    // 책 추가 메서드
    void addBook(const string& title, const string& author) {
        books.push_back(Book(title, author)); // push_back 사용
        cout << "책이 추가되었습니다: " << title << " by " << author << endl;
    }

    // 모든 책 출력 메서드
    void displayAllBooks() const {
        if (books.empty()) {
            cout << "현재 등록된 책이 없습니다." << endl;
            return;
        }

        cout << "현재 도서 목록:" << endl;
        for (size_t i = 0; i < books.size(); i++) { // 일반적인 for문 사용
            cout << "- " << books[i].title << " by " << books[i].author << endl;
        }
    }

    // 책 제목으로 책 가져오기
    Book* getBookByTitle(const string& title)
    {
        return getBook(title);
    }

    // 저자명으로 책 가져오기
    Book* getBookByAuthor(const string& author)
    {
        return getBook(author, false);
    }

    // 책 제목으로 검색
    void searchByTitle(const string& title)
    {
        searchBook(title);
    }

    // 책 저자명으로 검색
    void searchByAuthor(const string& author)
    {
        searchBook(author, false);
    }

    // 검색 로직
    // search = 검색 내용 / isTitle = 제목 검색 인지 확인 (false = 저자명 검색)
    void searchBook(const string& search, const bool& isTitle = true)
    {
        string typeText = isTitle ? "책 제목" : "저자명";

        Book* book = getBook(search, isTitle);

        if (book != nullptr)
        {
            cout << "\n====입력한 " << typeText << "과 일치하는 책을 찾았습니다.====" << endl;
            cout << book->title << " by " << book->author << endl;
        }
    }

    // 해당하는 책 가져오기
    // search = 검색 내용 / isTitle = 제목 검색 인지 확인 (false = 저자명 검색)
    Book* getBook(const string& search, const bool& isTitle = true)
    {
        for (int i = 0; i < books.size(); i++)
        {
            string target = isTitle ? books[i].title : books[i].author;

            if (target == search)
            {
                return &books[i];
            }
        }

        cout << "\n====해당하는 책이 없습니다.====" << endl;
        return nullptr;
    }
};

class BorrowManager
{
private:
    // 재고 확인 변수
    unordered_map<string, int> stock;

    // 대여 여부 확인 변수
    unordered_map<string, bool> borrowed;
public:
    // 재고 초기화
    void initializeStock(Book book, int quantity = 3)
    {
        stock[book.title] = quantity; // 재고 초기화
        borrowed[book.title] = false; // 대여 여부 초기화
        cout << "\"" << book.title << "\" 재고 : " << quantity << "권" << endl;
    }

    // 책 제목으로 대여
    void borrowBook(string title)
    {
        // 해당 책의 재고가 0보다 크고
        if (stock[title] > 0)
        {
            cout << "\n\"" << title << "\" 재고 : " << stock[title] << "권" << endl;

            // 대여 여부가 false일 때 대여가능
            if (!borrowed[title])
            {
                while (true)
                {
                    cout << "\n\"" << title << "\"을(를) 대여 하시겠습니까?" <<  endl;
                    cout << "1. 대여" << endl;
                    cout << "2. 취소" << endl;
                    cout << "선택: ";

                    int choice; // 사용자의 메뉴 선택을 저장
                    cin >> choice;

                    if (choice == 1)
                    {
                        stock[title]--; // 재고 감소
                        borrowed[title] = true; // 대여 여부 변경

                        cout << "\n대여가 완료 되었습니다." << endl;
                        cout << "남은 재고 :" << stock[title] << "권" << endl;
                        break;
                    }
                    else if(choice == 2)
                    {
                        break;
                    }
                    else
                    {
                        cout << "\n잘못된 입력입니다. 다시 시도하세요." << endl;
                    }
                }
            }
            // 이미 대여 중인 경우
            else
            {
                cout << "\n이미 대여 중인 책입니다." << endl;
            }
        }
        // 재고가 없을 경우
        else
        {
            cout << "\n\"" << title << "\"의 재고가 없어서 대여가 불가능합니다." << endl;
        }
    }

    // 빌린 책 목록 가져오기
    unordered_map<int, string> getBorrowedStock()
    {
        // 빌린 책을 담을 맵
        unordered_map<int, string> borrowedBook;

        if (stock.empty())
        {
            cout << "\n현재 재고가 없습니다." << endl;
            return borrowedBook;
        }

        bool displayText = false;

        // 빌린 책 카운트
        int id = 1;
        for (pair<string, int> book : borrowed)
        {
            if (book.second)
            {
                if (!displayText)
                {
                    cout << "\n현재 대여 중인 책 목록" << endl;
                    displayText = true;
                }

                cout << id << ". " << book.first << endl;

                borrowedBook[id] = book.first;
                id++;
            }
        }

        if (borrowedBook.empty())
        {
            cout << "\n현재 대여 중인 책이 없습니다." << endl;
        }

        return borrowedBook;
    }

    // 책 반납
    void returnBook()
    {
        while (true)
        {
            unordered_map<int, string> borrowedBook = getBorrowedStock();
            if (!borrowedBook.empty())
            {
                int size = borrowedBook.size();

                cout << size + 1 << ". 취소" << endl;
                cout << "반납할 책 선택: ";

                int choice; // 사용자의 메뉴 선택을 저장
                cin >> choice;

                if (1 <= choice && choice <= size)
                {
                    string title = borrowedBook[choice];

                    stock[title]++; // 재고 증가
                    borrowed[title] = false; // 대여 여부 변경

                    cout << "\n반납이 완료 되었습니다." << endl;
                    cout << "남은 재고 :" << stock[title] << "권" << endl;

                    break;
                }
                else if (choice == size + 1)
                {
                    break;
                }
                else
                {
                    cout << "\n잘못된 입력입니다. 다시 시도하세요." << endl;
                }
            }
            // 대여 중인 책이 없을 경우
            else
            {
                break;
            }
        }
    }

    // 모든 재고 출력
    void displayStock()
    {
        if (stock.empty())
        {
            cout << "\n현재 재고가 없습니다." << endl;
            return;
        }

        cout << "\n현재 재고 목록:" << endl;
        for (pair<string,int> book : stock)
        {
            cout << "- " << book.first << " : " << book.second << "권" << endl;
        }
    }
};

int main() {
    BookManager manager;
    BorrowManager borrow;

    // 도서관 관리 프로그램의 기본 메뉴를 반복적으로 출력하여 사용자 입력을 처리합니다.
    // 프로그램 종료를 선택하기 전까지 계속 동작합니다.
    while (true) {
        cout << "\n도서관 관리 프로그램" << endl;
        cout << "1. 책 추가" << endl; // 책 정보를 입력받아 책 목록에 추가
        cout << "2. 모든 책 출력" << endl; // 현재 책 목록에 있는 모든 책 출력
        cout << "3. 책 제목으로 검색" << endl;
        cout << "4. 저자명으로 검색" << endl;
        cout << "5. 모든 책 재고 출력" << endl;
        cout << "6. 책 제목으로 대여" << endl;
        cout << "7. 저자명으로 대여" << endl;
        cout << "8. 책 반납" << endl;
        cout << "9. 종료" << endl; // 프로그램 종료
        cout << "선택: ";

        int choice; // 사용자의 메뉴 선택을 저장
        cin >> choice;

        if (choice == 1)
        {
            // 1번 선택: 책 추가
            // 사용자로부터 책 제목과 저자명을 입력받아 BookManager에 추가합니다.
            string title, author;
            cout << "책 제목: ";
            cin.ignore(); // 이전 입력의 잔여 버퍼를 제거
            getline(cin, title); // 제목 입력 (공백 포함)
            cout << "책 저자: ";
            getline(cin, author); // 저자명 입력 (공백 포함)
            manager.addBook(title, author); // 입력받은 책 정보를 추가
            borrow.initializeStock(Book(title, author));
        }
        else if (choice == 2) {
            // 2번 선택: 모든 책 출력
            // 현재 BookManager에 저장된 책 목록을 출력합니다.
            manager.displayAllBooks();
        }
        else if (choice == 3) {
            // 3번 선택: 책 제목으로 검색
            // 사용자로부터 책 제목을 입력받아 BookManager에서 검색
            string title;
            cout << "검색할 책 제목: ";
            cin.ignore(); // 이전 입력의 잔여 버퍼를 제거
            getline(cin, title); // 책 제목 입력 (공백 포함)
            manager.searchByTitle(title);
        }
        else if (choice == 4)
        {
            // 4번 선택: 저자명으로 검색
            // 사용자로부터 저자명을 입력받아 BookManager에서 검색
            string author;
            cout << "검색할 저자명: ";
            cin.ignore(); // 이전 입력의 잔여 버퍼를 제거
            getline(cin, author); // 저자명 입력 (공백 포함)
            manager.searchByAuthor(author);
        }
        else if (choice == 5)
        {
            // 5번 선택: 모든 책 재고 출력
            // 현재 BorrowManager에 저장된 책 재고 목록을 출력합니다.
            borrow.displayStock();
        }
        else if (choice == 6)
        {
            // 5번 선택: 책 제목으로 대여
            // 사용자로부터 책 제목을 입력받아
            // BookManager에서 검색하여 책이 있고,
            // BorrowManager에 저장된 책 재고와 대여 여부를 판단하여 
            // 대여가 가능하다면 대여
            string title;
            cout << "대여할 책 제목: ";
            cin.ignore(); // 이전 입력의 잔여 버퍼를 제거
            getline(cin, title); // 책 제목 입력 (공백 포함)
            if (manager.getBookByTitle(title) != nullptr)
            {
                borrow.borrowBook(title);
            }
        }
        else if (choice == 7)
        {
            // 5번 선택: 저자명으로 대여
            // 사용자로부터 저자명을 입력받아
            // BookManager에서 검색하여 책이 있고,
            // BorrowManager에 저장된 책 재고와 대여 여부를 판단하여 
            // 대여가 가능하다면 대여
            string author;
            cout << "대여할 책의 저자명: ";
            cin.ignore(); // 이전 입력의 잔여 버퍼를 제거
            getline(cin, author); // 책 제목 입력 (공백 포함)

            Book* book = manager.getBookByAuthor(author);
            if (book != nullptr)
            {
                string title = book->title;
                borrow.borrowBook(title);
            }
        }
        else if (choice == 8)
        {
            // 5번 선택: 책 반납
            // 사용자에게 대여중인 책 목록을 보여주고
            // 책에 해당하는 인덱스를 입력받아 해당 책을 반납
            borrow.returnBook();
        }
        else if (choice == 9)
        {
            // 5번 선택: 종료
            // 프로그램을 종료하고 사용자에게 메시지를 출력합니다.
            cout << "프로그램을 종료합니다." << endl;
            break; // while 루프 종료
        }
        else
        {
            // 잘못된 입력 처리
            // 메뉴에 없는 번호를 입력했을 경우 경고 메시지를 출력합니다.
            cout << "잘못된 입력입니다. 다시 시도하세요." << endl;
        }
    }

    return 0; // 프로그램 정상 종료
}
```

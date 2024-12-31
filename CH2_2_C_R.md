
# [4번 과제] CH 2 C++ Summary
## 필수 기능
기존 도서관 관리 프로그램에 검색 기능을 추가해주세요

- 책 제목으로 검색이 가능해야 합니다.
- 작가로 검색이 가능해야 합니다.
- 책 제목이 동일한 경우는 없다고 가정 합니다.

필수 기능 구현 완료 후 전체적인 구조를 그리면 아래와 같습니다.

어디까지나 참조일 뿐 꼭 이대로 구현할 필요는 없습니다.

![image](https://github.com/user-attachments/assets/669646c0-d71f-4707-8e6f-173054fc0f54)

## 평가 기준
- 책 제목으로 검색 시 정확히 결과가 반환되는지 확인
- 작가로 검색 시 결과가 올바르게 표시되는지 확인
- 객체지향적 설계 원칙(클래스와 메서드 활용)을 얼마나 잘 이해했는지
- 검색 기능의 논리적 흐름과 데이터 접근 방식이 적절한지
- 코드의 가독성, 함수 분리 여부, 중복 코드 최소화 여부
- 검색 기능에서 입력 검증 처리와 에러 처리 여부

```c#
#include <iostream>
#include <vector>
#include <string>

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

    // 책 제목으로 검색
    void searchByTitle(string title)
    {
        cout << endl << "입력한 책 제목: " << title << endl;
        for (int i = 0; i < books.size(); i++)
        {
            if (books[i].title == title)
            {
                cout << endl << "====입력한 책 제목과 일치하는 책을 찾았습니다.====" << endl;
                cout << books[i].title << " by " << books[i].author << endl;
                return;
            }
        }

        cout << "====검색 결과가 없습니다.====" << endl;
    }

    // 책 저자명으로 검색
    void searchByAuthor(string author)
    {
        cout << endl << "입력한 저자명: " << author << endl;
        for (int i = 0; i < books.size(); i++)
        {
            if (books[i].author == author)
            {
                cout << endl << "====입력한 저자명과 일치하는 책을 찾았습니다.====" << endl;
                cout << books[i].title << " by " << books[i].author << endl;
                return;
            }
        }

        cout << "====검색 결과가 없습니다.====" << endl;
    }
};

int main() {
    BookManager manager;

    // 도서관 관리 프로그램의 기본 메뉴를 반복적으로 출력하여 사용자 입력을 처리합니다.
    // 프로그램 종료를 선택하기 전까지 계속 동작합니다.
    while (true) {
        cout << "\n도서관 관리 프로그램" << endl;
        cout << "1. 책 추가" << endl; // 책 정보를 입력받아 책 목록에 추가
        cout << "2. 모든 책 출력" << endl; // 현재 책 목록에 있는 모든 책 출력
        cout << "3. 책 제목으로 검색" << endl;
        cout << "4. 저자명으로 검색" << endl;
        cout << "5. 종료" << endl; // 프로그램 종료
        cout << "선택: ";

        int choice; // 사용자의 메뉴 선택을 저장
        cin >> choice;

        if (choice == 1) {
            // 1번 선택: 책 추가
            // 사용자로부터 책 제목과 저자명을 입력받아 BookManager에 추가합니다.
            string title, author;
            cout << "책 제목: ";
            cin.ignore(); // 이전 입력의 잔여 버퍼를 제거
            getline(cin, title); // 제목 입력 (공백 포함)
            cout << "책 저자: ";
            getline(cin, author); // 저자명 입력 (공백 포함)
            manager.addBook(title, author); // 입력받은 책 정보를 추가
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
            // 5번 선택: 종료
            // 프로그램을 종료하고 사용자에게 메시지를 출력합니다.
            cout << "프로그램을 종료합니다." << endl;
            break; // while 루프 종료
        }
        else {
            // 잘못된 입력 처리
            // 메뉴에 없는 번호를 입력했을 경우 경고 메시지를 출력합니다.
            cout << "잘못된 입력입니다. 다시 시도하세요." << endl;
        }
    }

    return 0; // 프로그램 정상 종료
}
```

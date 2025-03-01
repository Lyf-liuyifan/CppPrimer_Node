#include "Sales_item.h"

int main(void){
    // Sales_item item1(std::string("C++Primer")), item2(std::string("C++Primer"));
    // std::cin >> item1 >>item2;
    // std::cout << item1 + item2 << std::endl;
    using std::cout;
    using std::cin;
    using std::endl;
    Sales_item item;
    std::cin >> item;
    // cin.get();
    for(Sales_item temp_item; std::cin >> temp_item; std::cout << item){
        if(item.isbn() == temp_item.isbn()){
            item += temp_item;
        }else{
            std::cout << "different bookNo" << endl;
            break;
        }
        // cin.get();
    }
    cout << item;
    return 0;
}

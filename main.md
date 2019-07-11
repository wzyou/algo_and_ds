```C++
#include <iostream>
#include <string>
#include <cassert>
#include <cstdint>
#include <cstdio>

using namespace std;


class DateTime {
public:
    DateTime():year(0), month(0){}
    DateTime(string s) {
        assert(s.length() == 6);
        sscanf(s.c_str() + 4,"%d", &month);
        s.resize(4);
        sscanf(s.c_str(), "%d", &year);
#if 0
        if (month < 1 || month > 12 || year < 0) {
            month = -1;
            year = -1;
        }
#endif
    }
    int getMonths() const {
        return month + year * 12;
    }


    bool operator>(const DateTime& d) const {
        if (this->getMonths() > d.getMonths()) return true;
        return false;
    }
    bool operator<(const DateTime& d) const {
        if (this->getMonths() < d.getMonths()) return true;
        return false;
    }
    bool operator==(const DateTime& d) const {
        if (!(*this > d) && !(*this < d)) return true;
        return false;
    }

    void addMonth(int months) {
        month += months;
        year += month / 12;
        month %= 12;
    }

    void delMonths(int months) {
        month -= months;
        year -= month < 0?1:0;
        month = month < 0?month + 12:month;
    }
    string ToString() const {
        char buff[7];
        sprintf(buff,"%04d",year);
        sprintf(buff+4, "%02d", month);
        return buff;
    }
private:
    int32_t year;
    int32_t month;
};


class UtilDateTime {
public:
    UtilDateTime(const DateTime& begin, const DateTime& end):beginDay(begin), endDay(end) {
        assert(endDay > beginDay);
    }

    ~UtilDateTime(){}

    string GetTheDays(DateTime d) {
        string s;
        s.reserve(10);
        if (d > endDay || d < beginDay) {
            if (d.getMonths() - endDay.getMonths() > 3 || beginDay.getMonths() - d.getMonths() > 3) {
                return "0 0";
            }
            if (d > endDay) {
                return beginDay.ToString() + " 0";
            }
            return string("0 ") + endDay.ToString();
        } else {
            int months = d.getMonths() - beginDay.getMonths();
            if (months % 3) {
                DateTime d2 = beginDay;
                d2.addMonth(months - months%3);
                string s;
                s += d2.ToString() + " ";
                d2.addMonth(3);
                s += !(d2 > endDay) ? d2.ToString():endDay.ToString();
                return s;
            } else {
                string s = d.ToString();
                if (beginDay == d) {
                    return "0 " + s;
                }
                d.delMonths(3);
                return d.ToString() + " " + s;
            }
        }
    }
private:
    const DateTime beginDay;
    const DateTime endDay;

};


string fun(const string& s) {
    UtilDateTime udt(string(s.c_str(), 6), string(s.c_str() + 7, 6));
    return udt.GetTheDays(string(s.c_str() + 14, 6));

}

int main() {
    //样例
    cout << fun("201801 202012 201802") << endl;
    cout << endl;
    //左边界
    cout << fun("201801 202012 201801") << endl;
    cout << fun("201801 202012 201712") << endl; //
    cout << fun("201801 202012 201701") << endl;
    cout << endl;
    //右边界
    cout << fun("201801 202012 202011") << endl;
    cout << fun("201801 202012 202101") << endl; //
    cout << fun("201801 202012 202201") << endl;
    return 0;
}
```


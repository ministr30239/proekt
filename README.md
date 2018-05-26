#include <cstdio>
#include <string>
#include <vector>
#include <algorithm>

void check(bool e) {
    if (!e) {
        throw 1;
    }
}

void getMid(const int scale, const std::string &ticket1, const std::string &ticket2, std::string &mid1, std::string &mid2) {
    check(ticket1 <= ticket2);
    if (ticket1 == ticket2) {
        mid1 = "";
        mid2 = "";
        return;
    }
    check(ticket1.size() == ticket2.size());
    for (int i = 0; i < (int)ticket1.size(); i++) {
        if (ticket1[i] != ticket2[i]) {
            if (ticket2[i] != ticket1[i] + 1) {
                mid2 = "";
                mid1 = ticket1;
                mid1[i]++;
                for (int j = i + 1; j < (int)mid1.size(); j++) {
                    mid1[j] = '0';
                }
                check(ticket1 < mid1 && mid1 < ticket2);
                return;
            }
            else {
                check(ticket2[i] == ticket1[i] + 1);
                mid1 = ticket1;
                for (int j = i + 1; j < (int)mid1.size(); j++) {
                    mid1[j] =(char)('0' + scale - 1);
                }
                mid2 = ticket2;
                for (int j = i + 1; j < (int)mid2.size(); j++) {
                    mid2[j] = '0';
                }
                check(ticket1 <= mid1 && mid1 < mid2 && mid2 <= ticket2);
                return;
            }
        }
    }
    throw 1;
}

int commonPrefix(const std::string &s1, const std::string &s2) {
    check(s1.size() == s2.size());
    for (int i = 0; i < (int)s1.size(); i++) {
        if (s1[i] != s2[i]) {
        return i;
     }
    }
    return (int)s1.size();
}

void checkTicket(const std::string &ticket, const std::vector<std::string> &tickets, const std::vector<int> &win, long long &answer){
    static std::vector<int> levelLen(win.size(), 0);
    static std::vector<int> afterLevel(win.size(), 0);
    static int pos = 0;
    static std::string prevTicket = "";
    check(ticket >= prevTicket);
    prevTicket = ticket;
    while (pos + 1 < (int)tickets.size() && tickets[pos] < ticket && tickets[pos + 1]  <= ticket) {
        pos++;
    }
    if (pos + 1 < (int)tickets.size() && commonPrefix(tickets[pos + 1], ticket) > commonPrefix(tickets[pos], ticket)) {
        pos++;
    }

    for (int level = 0; level < (int)win.size(); level++) {
        if (pos < afterLevel[level]) {
      if (levelLen[level] != 0){
           if (tickets[afterLevel[level] - levelLen[level]][level] != ticket[level]) {
           levelLen[level] = 0;
           }

          }


       } else {
           if (pos != afterLevel[level]){
                //printf("ticket = %s pos = %d level = %d afterLevel[level] = %d \n", ticket.c_str(), pos, level, afterLevel[level]);
                check(pos == afterLevel[level]);
           }
            levelLen[level] = 0;
            while (
                ((level == 0 && afterLevel[level] < (int)tickets.size()) ||
                (level  > 0 && afterLevel[level] < afterLevel[level - 1]))
                && tickets[afterLevel[level]][level] == ticket[level]
                ) {
                afterLevel[level]++;
                levelLen[level]++;
            }

        }
    }
    //printf("ticket = %s ", ticket.c_str());
    //for (int level = 0; level < (int)win.size(); level++) {
      // printf("levelLen[%d]=%d ", level, levelLen[level]);
    //}
    long long sum = 0;
    for (int level = 0; level + 1 < (int)win.size(); level++) {
        sum += 1LL * (levelLen[level] - levelLen[level + 1]) * win[level];
    }
    sum += 1LL * levelLen.back() * win.back();
    //printf("sum=%d\n", sum);
    answer = std::min( answer, sum);

}

int main() {
    int nTickets, len, scale;
    scanf("%d %d %d", &nTickets, &len, &scale);
std::vector<int> win(len);
    for (int i = 0; i < len; i++) {
        scanf("%d", &win[i]);
    }
    std::vector<std::string> tickets(nTickets);
    for (int i = 0; i < nTickets; i++) {
        char buffer[10 + 1] = {};
        scanf("%10s", buffer);
        tickets[i] = buffer;
    }
    long long answer = 1LL * win.back() * nTickets;
    {
        std::string ticket(len, '0');
        checkTicket(ticket, tickets, win, answer);

    }
    for (int i = 0; i < nTickets - 1; i++) {
        checkTicket(tickets[i], tickets, win, answer);
        std::string mid1, mid2;
        getMid(scale,tickets[i], tickets[i + 1], mid1, mid2);

    if (mid1 != "") {
        checkTicket(mid1, tickets, win, answer);
    }
    if (mid2 != "") {
        checkTicket(mid2, tickets, win, answer);
    }

    }
    checkTicket(tickets.back(), tickets, win, answer);
 {
    std::string ticket(len, (char)('0' + scale - 1));
    checkTicket(ticket, tickets, win, answer);
 }
printf ("\n answer is %3d\n ", answer);


return 0;

}

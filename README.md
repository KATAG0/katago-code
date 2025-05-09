#define _CRT_SECURE_NO_WARNINGS
#include<stdio.h>
#define SIZE 20


void reserveSeat(int list[], int reservationnumbers[], int* yeahyarkhyunghuang);
void checkReservation(int list[], int reservationnumbers[]);
void changeReservation(int list[], int reservationnumbers[]);
void cancelReservation(int list[], int reservationnumbers[], int* yeahyarkhyunghuang);
void printReservationStatus(int list[], int reservationnumbers[], int yeahyarkhyunghuang);
int validateSeatNumber(int seatNumber); 
int validateCustomerNumber(int customerNumber);

int main() {
    int select;
    int list[SIZE] = { 0 }; // 모든 좌석을 0으로 초기화 (0: 예약되지 않음, 1: 예약됨)
    int reservationnumbers[SIZE] = { 0 }; // 각 좌석에 대응하는 번호 저장
    int yeahyarkhyunghuang = 0; // 예약된 좌석 수

    while (1) { // 무한 루프
        printf("좌석예약 :1, 예약좌석확인 : 2, 예약좌석 변경 : 3, 예약좌석 취소 : 4, 종료 : 9\n");
        scanf("%d", &select);

        switch (select) {
        case 1:
            reserveSeat(list, reservationnumbers, &yeahyarkhyunghuang);
            break;
        case 2:
            checkReservation(list, reservationnumbers);
            break;
        case 3:
            changeReservation(list, reservationnumbers);
            break;
        case 4:
            cancelReservation(list, reservationnumbers, &yeahyarkhyunghuang);
            break;
        case 9:
            printReservationStatus(list, reservationnumbers, yeahyarkhyunghuang);
            return 0;
        default:
            printf("잘못된 입력입니다. 다시 시도하세요.\n");
        }
    }
}

// 좌석 예약
void reserveSeat(int list[], int reservationnumbers[], int* yeahyarkhyunghuang) {
    int select_seat;
    printf("예약할 좌석 번호를 입력하세요 (1 ~ %d): ", SIZE);
    scanf("%d", &select_seat);

    if (!validateSeatNumber(select_seat)) {
        printf("잘못된 좌석 번호입니다. 다시 시도하세요.\n");
        return;
    }

    if (list[select_seat - 1] == 1) { // 배열 인덱스는 0부터 시작
        printf("%d번 좌석은 이미 예약되었습니다. 다른 좌석을 선택하세요.\n", select_seat);
    }
    else {
        list[select_seat - 1] = 1; // 좌석 예약
        printf("고객님의 고객번호를 입력하세요: ");
        scanf("%d", &reservationnumbers[select_seat - 1]);

        while (!validateCustomerNumber(reservationnumbers[select_seat - 1])) {
            printf("1~99사이의 수를 다시 입력하세요.\n");
            scanf("%d", &reservationnumbers[select_seat - 1]);
        }
        printf("%d번 좌석이 예약되었습니다. 고객번호는 %d입니다.\n", select_seat, reservationnumbers[select_seat - 1]);
        (*yeahyarkhyunghuang)++;
    }
}

// 예약 상태 출력
void printReservationStatus(int list[], int reservationnumbers[], int yeahyarkhyunghuang) {
    printf("프로그램을 종료합니다.\n");
    printf("예약된 좌석은 %d개 입니다.\n", yeahyarkhyunghuang);

    if (yeahyarkhyunghuang > 0) {
        printf("예약된 좌석과 고객 번호는 다음과 같습니다:\n");
        for (int i = 0; i < SIZE; i++) {
            if (list[i] != 0) { // 예약된 좌석만 출력
                printf("좌석 번호: %d, 고객 번호: %d\n", i + 1, reservationnumbers[i]);
            }
        }
    }
    else {
        printf("현재 예약된 좌석이 없습니다.\n");
    }
}

// 예약 좌석 확인
void checkReservation(int list[], int reservationnumbers[]) {
    int input_customernumber;
    printf("고객님의 고객번호를 입력하세요: ");
    scanf("%d", &input_customernumber);

    int found = 0;
    for (int i = 0; i < SIZE; i++) {
        if (list[i] == 1 && reservationnumbers[i] == input_customernumber) {
            printf("예약된 좌석은 %d번입니다.\n", i + 1); // 배열 인덱스를 좌석 번호로 변환
            found = 1;
            break;
        }
    }

    if (!found) {
        printf("예약된 좌석이 없습니다.\n");
    }
}

// 예약 좌석 변경
void changeReservation(int list[], int reservationnumbers[]) {
    int input_customernumber, select_seat;
    printf("고객님의 고객번호를 입력하세요: ");
    scanf("%d", &input_customernumber);

    int found = 0;
    for (int i = 0; i < SIZE; i++) {
        if (list[i] == 1 && reservationnumbers[i] == input_customernumber) {
            printf("현재 예약된 좌석은 %d번입니다.\n", i + 1);
            printf("변경할 좌석 번호를 입력하세요 (1 ~ %d): ", SIZE);
            scanf("%d", &select_seat);

            if (!validateSeatNumber(select_seat)) {
                printf("잘못된 좌석 번호입니다. 다시 시도하세요.\n");
                return;
            }

            if (list[select_seat - 1] == 1) {
                printf("%d번 좌석은 이미 예약되었습니다. 다른 좌석을 선택하세요.\n", select_seat);
            }
            else {
                list[i] = 0; // 기존 좌석 해제
                list[select_seat - 1] = 1; // 새로운 좌석 예약
                reservationnumbers[select_seat - 1] = input_customernumber;
                printf("%d번 좌석으로 변경되었습니다.\n", select_seat);
            }
            found = 1;
            break;
        }
    }

    if (!found) {
        printf("예약된 좌석이 없습니다.\n");
    }
}

// 예약 좌석 취소
void cancelReservation(int list[], int reservationnumbers[], int* yeahyarkhyunghuang) {
    int input_customernumber;
    printf("고객님의 고객번호를 입력하세요: ");
    scanf("%d", &input_customernumber);

    int found = 0;
    for (int i = 0; i < SIZE; i++) {
        if (list[i] == 1 && reservationnumbers[i] == input_customernumber) {
            list[i] = 0; // 좌석 해제
            reservationnumbers[i] = 0; // 고객번호 초기화
            printf("%d번 좌석 예약이 취소되었습니다.\n", i + 1);
            (*yeahyarkhyunghuang)--;
            found = 1;
            break;
        }
    }

    if (!found) {
        printf("예약된 좌석이 없습니다.\n");
    }
}

// 좌석 번호 겹치는지 검사
int validateSeatNumber(int seatNumber) {
    return seatNumber >= 1 && seatNumber <= SIZE; // 좌석 번호는 1부터 시작
}

// 고객 번호 겹치는지 검사 함수
int validateCustomerNumber(int customerNumber) {
    return customerNumber > 0 && customerNumber < 100;
}

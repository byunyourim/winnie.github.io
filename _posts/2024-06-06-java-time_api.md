---
layout: post
title: Time API
categories: [Java]
---

# Time API

날짜와 시간을 다루기 위한 API로 자바 8에서 도입되었습니다.



<br>



## 주요 클래스
- **LocalDate**: 날짜를 표현하는 클래스 (예: 2024-08-11)

- **LocalTime**: 시간(시, 분, 초)을 표현하는 클래스 (예: 14:30:00)
  
- **LocalDateTime**: 날짜와 시간을 모두 표현하는 클래스 (예: 2024-08-11T14:30:00)
  
- **ZonedDateTime**: 시간대 정보를 포함하는 날짜와 시간 클래스 
  
- **Duration**: 두 시점 간의 시간 차이를 표현하는 클래스 
  
- **Period**: 두 날짜 간의 차이를 연, 월, 일 단위로 표현하는 클래스 
  
- **DateTimeFormatter**: 날짜와 시간을 문자열로 포맷하거나 문자열을 날짜와 시간으로 파싱하는 클래스

```java
public class TimeApiExample {
    public static void main(String[] args) {
        // 현재 날짜
        LocalDate today = LocalDate.now();
        System.out.println("오늘 날짜: " + today);

        // 현재 시간
        LocalTime now = LocalTime.now();
        System.out.println("현재 시간: " + now);

        // 현재 날짜와 시간
        LocalDateTime dateTime = LocalDateTime.now();
        System.out.println("현재 날짜와 시간: " + dateTime);
        
        // 날짜 연산
        LocalDate today = LocalDate.now();
        LocalDate futureDate = today.plus(Period.ofDays(10));
        System.out.println("10일 후 날짜: " + futureDate);

        // 시간 연산
        LocalDateTime now = LocalDateTime.now();
        LocalDateTime later = now.plus(Duration.ofHours(5));
        System.out.println("5시간 후 시간: " + later);
        
        // 날짜와 시간 포맷팅
        LocalDateTime now = LocalDateTime.now();
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
        String formattedDateTime = now.format(formatter);
        System.out.println("포맷팅된 날짜와 시간: " + formattedDateTime);

        // 문자열을 날짜와 시간으로 파싱
        String dateTimeString = "2024-08-11 15:30:00";
        LocalDateTime parsedDateTime = LocalDateTime.parse(dateTimeString, formatter);
        System.out.println("파싱된 날짜와 시간: " + parsedDateTime);
    
    }
}
```


<br>


## LocalDate 클래스
날짜 정보를 표현하며, 시간 정보는 포함되지 않습니다.  

- **now()**: 현재 날짜 반환
  
- **of(int year, int month, int dayOfMonth)**: 특정 날짜 생성
  
- **plusDays(long daysToAdd)**: 날짜 + 일수 
  
- **minusMonths(long monthsToSubtract)**: 날짜 - 월수

- **getDayOfWeek()**: 요일 반환

- **parse(CharSequence text)**: 문자열을 LocalDate 로 변환

- **format(DateTimeFormatter formatter)**: LocalDate를 지정한 형식으로 포맷팅하여 문자열로 반환

- **with(TemporalField field, long newValue)**: 특정 필드의 값을 변경한 새로운 LocalDate 객체를 반환

- **isBefore(LocalDate otherDate) & isAfter(LocalDate otherDate)**: 두 날짜를 비교하여 현재 날짜가 다른 날짜보다 이전 또는 이후인지 확인

- **getDayOfYear()**: 해당 연도에서 몇 번째 날인지 반환

- **lengthOfMonth() & lengthOfYear(): 해당 월 또는 연도의 일 수 반환

```java
LocalDate date = LocalDate.of(2024, 8, 11); // 2024년 8월 11일
LocalDate tomorrow = date.plusDays(1); // 2024년 8월 12일

LocalDate date = LocalDate.parse("2024-08-11");
String formattedDate = date.format(DateTimeFormatter.ofPattern("dd/MM/yyyy"));

LocalDate firstDayOfMonth = date.with(ChronoField.DAY_OF_MONTH, 1);
boolean isBefore = date.isBefore(LocalDate.now());

int dayOfYear = date.getDayOfYear();
int daysInMonth = date.lengthOfMonth();
```


<br>



## LocalTime 클래스
시간 정보를 표현하며, 날짜 정보는 포함되지 않습니다.  

- **now()**: 현재 시간 반환

- **of(int hour, int minute, int second)**: 특정 시간 생성

- **plusHours(long hoursToAdd)**: 시간 + 시간 

- **minusMinutes(long minutesToSubtract)**: 시간 - 분 

- **getHour()**: 시 반환

- **toSecondOfDay() & toNanoOfDay()**: 하루의 시작부터 경과된 초 또는 나노초 반환
```java
LocalTime time = LocalTime.of(15, 30); // 15시 30분
LocalTime later = time.plusMinutes(15); // 15시 45분

// 두 시간을 비교하여 현재 시간이 다른 시간보다 이전 또는 이후인지 확인합니다
boolean isAfter = time.isAfter(LocalTime.NOON);
int secondsOfDay = time.toSecondOfDay();
```



<br>



## LocalDateTime 클래스
날짜와 시간 정보를 모두 표현합니다.

- **now()**: 현재 날짜와 시간 반환 

- **of(int year, int month, int dayOfMonth, int hour, int minute, int second)**: 특정 날짜와 시간 생성

- **plusDays(long daysToAdd)**: 날짜 + 일수

- **minusHours(long hoursToSubtract)**: 시간 - 시간수

- **getDate(), getTime()**: 날짜와 시간을 분리하여 반환

```java
LocalDateTime dateTime = LocalDateTime.of(2024, 8, 11, 15, 30); // 2024년 8월 11일 15시 30분
LocalDateTime futureDateTime = dateTime.plusHours(1); // 2024년 8월 11일 16시 30분

LocalDate date = dateTime.toLocalDate();
LocalTime time = dateTime.toLocalTime();
```











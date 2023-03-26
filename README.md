# UMT-Software-Test
from datetime import datetime, timedelta

def free_periods(calendar, limits,meeting_time):

    #Obtinem lista de intervale libere pentru fiecare calendar
    #Daca gasim o perioada libera, atunci verificam daca se incadreaza in perioada meeting time, folosind functia timedelta
    #Functia strptime converteste din string in format datetime
    #Busy_periods retine o lista a intervalelor ocupate
    #Free_periods se formeaza prin iterarea busy_periods, fiind initializata cu intervalul de timp dintre limita de start si prima perioada ocupata

    limits_start = datetime.strptime(limits[0], '%H:%M')
    limits_end = datetime.strptime(limits[1], '%H:%M')
    busy_periods = [(datetime.strptime(start, '%H:%M'), datetime.strptime(end, '%H:%M')) for [start, end] in calendar]
    free_periods = [(limits_start, busy_periods[0][0])]
    for i in range(len(busy_periods)-1):
        free_periods.append((busy_periods[i][1], busy_periods[i+1][0]))
    free_periods.append((busy_periods[-1][1], limits_end))
    free_periods = [(s.strftime('%H:%M'), e.strftime('%H:%M')) for s, e in free_periods if (e-s) >= timedelta(minutes=meeting_time)]
    return free_periods

def find_free_slots(L1, L2, meeting_time):

    #Realizam intersectarea celor 2 liste, L1 si L2
    #L1 si L2 sunt listele de perioade libere ale celor calendare.
    #Parcurgem simultan cele 2 liste, asemenator algoritmului de interclasare
    #Daca gasim un interval satisfacator, il adaugam la lista noastra finala ce va fi returnata
    #Inaintam in parcurgerea listei, mutand indicii i si j corespunzator

    i, j = 0, 0
    result = []
    while i < len(L1) and j < len(L2):
        start = max(L1[i][0], L2[j][0])
        end = min(L1[i][1], L2[j][1])
        start_time = datetime.strptime(start, '%H:%M')
        end_time = datetime.strptime(end, '%H:%M')
        duration = (end_time - start_time).total_seconds() / 60.0  # Durata în minute
        if duration >= meeting_time:
            result.append([start, end])
        if L1[i][1] < L2[j][1]:
            i += 1
        elif L2[j][1] < L1[i][1]:
            j += 1
        else:
            i += 1
            j += 1
    return result

def rezultat(calendar1,limits1,calendar2,limits2,meeting_time):
    l1 = free_periods(calendar1, limits1,meeting_time)
    l2 = free_periods(calendar2, limits2,meeting_time)
    available_time = find_free_slots(l1, l2, meeting_time)
    return available_time


if __name__ == '__main__':
    calendar1 = [['9:00', '10:30'], ['12:00', '13:00'], ['16:00', '18:00']]
    limits1 = ['9:00', '20:00']
    calendar2 = [['10:00', '12:30'], ['12:30', '13:00'], ['14:30', '15:31'], ['16:00', '17:00']]
    limits2 = ['10:00', '18:30']
    meeting_time = 30

    rezultat_final=rezultat(calendar1,limits1,calendar2,limits2,meeting_time)
    print(rezultat_final)


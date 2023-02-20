def bot_start(symbol, s_l, quantity, qr, pr, s):

    # Проверить существует ли позиция True / False
    # Если позиция существует отрыть новую позицию
    if not position_enable(symbol):
        s = 0
        print(f'Нет позиции { symbol}')
        print(f'Создаю новую позицию для {symbol} {s_l} {quantity} ')
        open_first_position(symbol, s_l, quantity, qr, pr)


    # Если позиция не существует

    else:

        print(f'S = {s}')

        check_and_close_orders(symbol)  # Закрыть все лимитные ордера
        position_get = get_open_position(symbol)  #
        get_enter_price = position_get[5]         # Получить Данные по открытой позиции
        get_s_l = position_get[0]                 #
        tp_pr = 0.0  # Объявить переменную для цены TP
        tp_save = 0.0  # Объявить переменную для цены TP
        print(f' Позиция существует {position_get}')
        if get_s_l == 'long':
            tp_pr = round(get_enter_price + (get_enter_price * delta_tp / 100), pr)
            open_tp(symbol, qr, pr, delta_tp)

        if get_s_l == 'short':

            tp_pr = round(get_enter_price - (get_enter_price * delta_tp / 100), pr)
            save_pr = round(get_enter_price + (get_enter_price * delta_save[s] / 100), pr)
            print(f'save_pr =  {save_pr}')

            open_tp(symbol, qr, pr, delta_tp)
            open_save_delta(symbol, qr, pr, delta_save[s])

        print(f'tp_pr = {tp_pr}')




        while True:

            get_pr = float(get_price_by_symbol(symbol, pr,))

            if get_s_l == 'long':
                print(get_s_l)
                print(f'enable_check_price_for_tp = {tp_pr} * (1 - {enable_check_price_tp} / 100)')
                enable_check_price_for_tp = round(tp_pr * (1 - enable_check_price_tp / 100), pr)
                print(f'enable_check_price_for_tp = {enable_check_price_for_tp}')
                print(f'Если {get_pr} > = {enable_check_price_for_tp} ')
                if get_pr >= enable_check_price_for_tp:
                    print(f'Проверить TP  \n Порог для проверки {enable_check_price_for_tp} \n текущая цена: {get_pr}')

                    tp_enable = getLimitOrderCount(symbol)[0]
                    print(f'tp_enable = {tp_enable}')
                    if tp_enable == 1:
                        print(f' tp установлен цена {tp_pr}')
                    else:

                        position_get = get_open_position(symbol)
                        get_s_l = position_get[0]
                        if len(get_s_l) == 0:
                            break
                        else:
                            open_tp(symbol, qr, pr, delta_tp)

            if get_s_l == 'short':
                enable_check_price_for_tp = round(tp_pr * (1 + enable_check_price_tp / 100), pr)
                enable_check_price_for_save = round(save_pr * (1 - enable_check_price_save / 100), pr)

                if get_pr <= enable_check_price_for_tp:
                    print(f'Проверить TP  \n Порог для проверки {enable_check_price_for_tp} \n текущая цена: {get_pr}')
                    tp_enable = getLimitOrderCount(symbol)[0]
                    print(f'tp_enable = {tp_enable}')
                    if tp_enable == 1:
                        print(f' tp установлен цена {tp_pr}')
                    else:

                        position_get = get_open_position(symbol)
                        get_s_l = position_get[0]
                        if len(get_s_l) == 0:
                            s = 0
                            check_and_close_orders(symbol)
                            break
                        else:
                            open_tp(symbol, qr, pr, delta_tp)
                print(f'enable_check_price_for_save = {enable_check_price_for_save}')



                if get_pr >= enable_check_price_for_save:
                    print(f'Проверить SAVE  \n Порог для проверки {enable_check_price_for_save} \n текущая цена: {get_pr}')
                    tp_enable = getLimitOrderCount(symbol)[1]
                    print(f'tp_enable = {tp_enable}')
                    if tp_enable == 1:
                        print(f' SAVE ЕСТЬ  установлен цена {tp_pr} s = {s}')

                    else:
                        s += 1

                        check_and_close_orders(symbol)
                        break



            print(f'get_pr = {get_pr}')
            time.sleep(1)
# kata-event-sourcing
Proviamo a modellare *passo-per-passo* un conto corrente usando event sourcing e CQRS.

Iniziamo da questo modello...

```csharp
public record ContoCorrente(Guid Id)
{
    public decimal Saldo { get; private set; } = 0;

    public void Accredita(decimal importo)
    {
        Saldo += importo;
    }
    
    public void Addebita(decimal importo)
    {
        Saldo -= importo;
    }
}
```

...e proviamo a implementare i seguenti requisiti.

## 1. Come correntista, vorrei vedere lo storico dei movimenti
**Tracciabilità** di ogni invocazione dei metod `Accredita` e `Addebita`

---

## 2. Come istituto di credito, vorrei poter concedere un fido a un correntista
**Validazione**: un addebito che porterebbe il saldo al di sotto del fido, deve fallire.

---

## 3. Come correntista vorrei poter impostare il mio nome
**Tutto è un evento!** Sfruttiamo gli eventi anche per operazioni non legate al saldo.

---

## 4. Come correntista, vorrei non perdere il mio patrimonio quando il server si riavvia
**Persistenza**: che cosa salviamo nel database? 🤔

---

## 5. Come correntista, vorrei quindi ritrovare tutti i miei dati quando accedo al mio conto
**Caricamento:** come ripristiniamo lo stato del `ContoCorrente` a partire dallo stream di eventi?

---

## 6. Come sviluppatore, vorrei separare il modello per la scrittura dal modello per la lettura
**CQRS**: possiamo fare in modo che la classe `ContoCorrente` si occupi solo di generare gli eventi? 🤔

---

## 7. Come sviluppatore, vorrei comunque poter testare il modello per la scrittura
**Testing**: come verifichiamo se la concessione di un fido sta funzionando se non possiamo più accedere alla proprietà `Fido`?

---

## 8. Come sviluppatore, vorrei che la responsabilità della "lettura" sia assolta da altre classi predisposte allo scopo
**Pubblicazione**: come fare in modo che gli eventi arrivino ad altre classi?

---

## 9. Come correntista, vorrei vedere lo storico dei movimenti (accrediti e addebiti)
**Projection**: una classe si occuperà di questo specifico compito

---

## 10. Come correntista, vorrei essere avvisato se il mio conto va in rosso
**Altra projection**: creiamo un'altra classe si occuperà di attuare questi effetti collaterali
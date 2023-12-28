#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#define maxvoitures 50
#include <stdbool.h>
typedef struct {
    int jj;
    int mm;
    int aa;
}Date;

typedef struct {
    char marque[50];
    char modele[50];
    int annee;
    char etat[50];
    int prix_par_jour;
}Voiture;

typedef struct {
    char client[50];
    Voiture *voiture;
    Date debut;
    Date fin;
}Location;
typedef struct {
    char client[50];
    Voiture *voiture;
    Date debut;
    Date fin;
} Reservation;
typedef struct {
    Location *locations;
    int nombreLocations;
    Reservation *reservations;
    int nombreReservations;
} Historique;


void afficherDescription(Voiture *voiture) {
    printf("Marque: %s\nModele: %s\nAnnee: %d\nEtat: %s\nPrix_par_jour: %d Dt", voiture->marque, voiture->modele, voiture->annee, voiture->etat,voiture->prix_par_jour);
}
void afficherHistorique(Historique *historique) {
    if (historique->nombreLocations == 0) {
        printf("Aucune location dans l'historique.\n");
        return;
    }

    printf("Historique des locations:\n");
    for (int i = 0; i < historique->nombreLocations; i++) {
        printf("Client: %s\n", historique->locations[i].client);
        printf("Voiture louee: %s %s\n", historique->locations[i].voiture->marque, historique->locations[i].voiture->modele);
        printf("Debut: %d-%d-%d\n", historique->locations[i].debut.aa, historique->locations[i].debut.mm, historique->locations[i].debut.jj);
        printf("Fin: %d-%d-%d\n", historique->locations[i].fin.aa, historique->locations[i].fin.mm, historique->locations[i].fin.jj);
        printf("\n");
    }
}




void afficherHistoriqueParMois(Historique *historique, int mois, int annee) {
    bool locationsTrouvees = false;

    if (historique->nombreLocations == 0) {
        printf("Aucune location dans l'historique.\n");
        return;
    }

    printf("Historique des locations pour le mois %d de l'annee %d:\n", mois, annee);
    for (int i = 0; i < historique->nombreLocations; i++) {
        if ((historique->locations[i].debut.aa == annee && historique->locations[i].debut.mm == mois) ||
            (historique->locations[i].fin.aa == annee && historique->locations[i].fin.mm == mois)) {
            locationsTrouvees = true;

            printf("Client: %s\n", historique->locations[i].client);
            printf("Voiture louee: %s %s\n", historique->locations[i].voiture->marque, historique->locations[i].voiture->modele);
            printf("Debut: %d-%d-%d\n", historique->locations[i].debut.aa, historique->locations[i].debut.mm, historique->locations[i].debut.jj);
            printf("Fin: %d-%d-%d\n", historique->locations[i].fin.aa, historique->locations[i].fin.mm, historique->locations[i].fin.jj);
            printf("\n");
        }
    }

    if (!locationsTrouvees) {
        printf("Aucune location dans cette date.\n");
    }
}

void afficherHistoriqueParJour(Historique *historique, int jour, int mois, int annee) {
    bool locationsTrouvees = false;

    if (historique->nombreLocations == 0) {
        printf("Aucune location dans l'historique.\n");
        return;
    }

    printf("Historique des locations pour le %d-%d-%d:\n", jour, mois, annee);
    for (int i = 0; i < historique->nombreLocations; i++) {
        if ((historique->locations[i].debut.aa < annee ||
             (historique->locations[i].debut.aa == annee && historique->locations[i].debut.mm < mois) ||
             (historique->locations[i].debut.aa == annee && historique->locations[i].debut.mm == mois && historique->locations[i].debut.jj <= jour)) &&
            (historique->locations[i].fin.aa > annee ||
             (historique->locations[i].fin.aa == annee && historique->locations[i].fin.mm > mois) ||
             (historique->locations[i].fin.aa == annee && historique->locations[i].fin.mm == mois && historique->locations[i].fin.jj >= jour))) {
            locationsTrouvees = true;

            printf("Client: %s\n", historique->locations[i].client);
            printf("Voiture louée: %s %s\n", historique->locations[i].voiture->marque, historique->locations[i].voiture->modele);
            printf("Debut: %d-%d-%d\n", historique->locations[i].debut.aa, historique->locations[i].debut.mm, historique->locations[i].debut.jj);
            printf("Fin: %d-%d-%d\n", historique->locations[i].fin.aa, historique->locations[i].fin.mm, historique->locations[i].fin.jj);
            printf("\n");
        }
    }

    if (!locationsTrouvees) {
        printf("Aucune location dans cette date.\n");
    }
}

void louerVoiture(Voiture *voiture, Historique *historique, const char *client, Date debut, Date fin) {
    if (strcmp(voiture->etat, "Disponible") != 0) {
        printf("Cette voiture n'est pas disponible pour la location.\n");
        return;
    }

    strcpy(voiture->etat, "En location");
    historique->locations = realloc(historique->locations, (historique->nombreLocations + 1) * sizeof(Location));
    strcpy(historique->locations[historique->nombreLocations].client, client);
    historique->locations[historique->nombreLocations].voiture = voiture;
    historique->locations[historique->nombreLocations].debut = debut;
    historique->locations[historique->nombreLocations].fin = fin;

    historique->nombreLocations++;
    printf("Location effectuee avec succes.\n");

}

void retournerVoiture(Voiture *voiture, Historique *historique, Date dateRetour) {
    strcpy(voiture->etat, "Disponible");
    for (int i = 0; i < historique->nombreLocations; i++) {
        if (historique->locations[i].voiture == voiture) {
            historique->locations[i].fin = dateRetour;
            printf("Retour de la voiture effectue avec succes.\n");
            return;
        }
    }

    printf("Erreur : Impossible de trouver la location correspondante dans l'historique.\n");
}
void afficherVoituresDisponibles(Voiture *voitures, int nombreVoitures) {
    printf("Voitures disponibles :\n");
    for (int i = 0; i < nombreVoitures; i++) {
        if (strcmp(voitures[i].etat, "Disponible") == 0) {
            printf("%d. %s %s\n", i + 1, voitures[i].marque, voitures[i].modele);
        }
    }
}


void supprimerVoitureEnPanne(Voiture *voitures, int *nombreVoitures) {
    for (int i = 0; i < *nombreVoitures; i++) {
        if (strcmp(voitures[i].etat, "En panne") == 0) {
            for (int j = i; j < *nombreVoitures - 1; j++) {
                voitures[j] = voitures[j + 1];
            }

            (*nombreVoitures)--;
            i--;
        }
    }
    printf("Voitures en panne supprimees avec succes.\n");
}
void modifierVoiture(Voiture *voiture) {
    printf("Nouvelle description de la voiture (marque modele annee prix_par_jour) : ");
    scanf("%s %s %d %d", voiture->marque, voiture->modele, &voiture->annee,&voiture->prix_par_jour);

    printf("Nouvel etat de la voiture : ");
    scanf("%s", voiture->etat);

    printf("Modification effectuee avec succes.\n");
}
int estDateValide(const Date *date) {
    if (date->mm >= 1 && date->mm <= 12 && date->jj >= 1 && date->jj <= 31) {
        return 1;
    } else {
        return 0;
    }
}
void confirmerReservation(Historique *historique, int choixReservation) {
    if (choixReservation >= 1 && choixReservation <= historique->nombreReservations) {
        Reservation *reservation = &historique->reservations[choixReservation - 1];

        if (strcmp(reservation->voiture->etat, "Reservee") == 0) {
            strcpy(reservation->voiture->etat, "En location");
            historique->locations = realloc(historique->locations, (historique->nombreLocations + 1) * sizeof(Location));
            strcpy(historique->locations[historique->nombreLocations].client, reservation->client);
            historique->locations[historique->nombreLocations].voiture = reservation->voiture;
            historique->locations[historique->nombreLocations].debut = reservation->debut;
            historique->locations[historique->nombreLocations].fin = reservation->fin;

            historique->nombreLocations++;
            printf("Location confirmee avec succes.\n");
            for (int i = choixReservation - 1; i < historique->nombreReservations - 1; i++) {
                historique->reservations[i] = historique->reservations[i + 1];
            }
            historique->nombreReservations--;
        } else {
            printf("Cette reservation ne peut pas etre confirmee. La voiture n'est pas reservee.\n");
        }
    } else {
        printf("Numero de reservation invalide.\n");
    }
}

void annulerReservation(Historique *historique, int choixReservation) {
    if (choixReservation >= 1 && choixReservation <= historique->nombreReservations) {
        Reservation *reservation = &historique->reservations[choixReservation - 1];
        for (int i = choixReservation - 1; i < historique->nombreReservations - 1; i++) {
            historique->reservations[i] = historique->reservations[i + 1];
        }
        historique->nombreReservations--;
        printf("Reservation annulee avec succes.\n");
    } else {
        printf("Numero de reservation invalide.\n");
    }
}

void afficherReservations(Historique *historique) {
    if (historique->nombreReservations == 0) {
        printf("Aucune reservation dans l'historique.\n");
        return;
    }

    printf("Historique des reservations:\n");
    for (int i = 0; i < historique->nombreReservations; i++) {
        printf("Numero de reservation : %d\n", i + 1);
        printf("Client: %s\n", historique->reservations[i].client);
        printf("Voiture reservee: %s %s\n", historique->reservations[i].voiture->marque, historique->reservations[i].voiture->modele);
        printf("Debut: %d-%d-%d\n", historique->reservations[i].debut.aa, historique->reservations[i].debut.mm, historique->reservations[i].debut.jj);
        printf("Fin: %d-%d-%d\n", historique->reservations[i].fin.aa, historique->reservations[i].fin.mm, historique->reservations[i].fin.jj);
        printf("\n");
    }
}


void reserverVoiture(Voiture *voiture, Historique *historique, const char *client, Date debut, Date fin) {
    if (strcmp(voiture->etat, "Disponible") == 0) {
        strcpy(voiture->etat, "Reservee");
        historique->reservations = realloc(historique->reservations, (historique->nombreReservations + 1) * sizeof(Reservation));
        strcpy(historique->reservations[historique->nombreReservations].client, client);
        historique->reservations[historique->nombreReservations].voiture = voiture;
        historique->reservations[historique->nombreReservations].debut = debut;
        historique->reservations[historique->nombreReservations].fin = fin;

        historique->nombreReservations++;
        printf("Réservation effectuée avec succès.\n");
    } else {
        printf("Cette voiture n'est pas disponible pour la réservation.\n");
    }
}
int main() {
    Voiture voitures[maxvoitures];
    voitures[0] = (Voiture){"Toyota","Corolla", 2020,"Disponible",90};
    voitures[1] = (Voiture){"Honda", "Civic", 2019,"Disponible",80};
    voitures[2] = (Voiture){"Ford", "Focus", 2021,"En panne",70};
    voitures[3] = (Voiture){"Volkswagen", "Golf", 2022, "Disponible", 95};
    voitures[4] = (Voiture){"Chevrolet", "Cruze", 2021, "Disponible", 85};
    voitures[5] = (Voiture){"Hyundai", "Elantra", 2020, "En panne", 75};
    voitures[6] = (Voiture){"Nissan", "Altima", 2022, "Disponible", 100};
    voitures[7] = (Voiture){"Mazda", "3", 2021, "Disponible", 88};
    voitures[8] = (Voiture){"Kia", "Forte", 2019, "Disponible", 78};

    int nombreVoitures =9;
    Historique historique;
    historique.locations =NULL;
    historique.nombreLocations = 0;

    int choix;
    char client[50];
    Date debut, fin, dateRetour,dateConfirmation;
    int mois,jour,annee;
    historique.locations = malloc(maxvoitures * sizeof(Location));
    historique.reservations = malloc(maxvoitures * sizeof(Reservation));
    do {
        printf("\n===== Menu =====\n");
        printf("1. Louer une voiture\n");
        printf("2. Retourner une voiture\n");
        printf("3. Afficher l'historique des locations\n");
        printf("4. Afficher l'historique des locations par mois\n");
        printf("5. Afficher l'historique des locations par jour\n");
        printf("6. Afficher la description d'une voiture\n");
        printf("7. Supprimer une voiture en panne\n");
        printf("8. Modifier la description et l'etat d'une voiture\n");
        printf("9. Reserver voiture\n");
        printf("10. Confirmer reservation\n");
        printf("11. Annuler reservation\n");
        printf("12. Afficher les reservations\n");
        printf("13. Quitter\n");

        printf("Entrez votre choix : ");
        if (scanf("%d", &choix) != 1) {
            printf("Erreur de saisie. Veuillez entrer un nombre.\n");
            while (getchar() != '\n');
            continue;
        }
        switch (choix) {
            case 1:
                printf("Entrez le nom du client : \n");
                scanf(" %s", client);



             do {
            printf("Entrez la date de debut (jj mm aa) :\n ");
            if (scanf("%d %d %d", &debut.jj, &debut.mm, &debut.aa) != 3 || !estDateValide(&debut)) {
                printf("Format de date invalide. Veuillez entrer une date valide sous la forme (jj mm aa).\n");
                while (getchar() != '\n');}
            } while (!estDateValide(&debut));

             do {
            printf("Entrez la date de fin (jj mm aa) : \n");
            if (scanf("%d %d %d", &fin.jj, &fin.mm, &fin.aa) != 3 || !estDateValide(&fin)) {
                printf("Format de date invalide. Veuillez entrer une date valide sous la forme (jj mm aa).\n");
                while (getchar() != '\n');
            }
        } while (!estDateValide(&fin));



                printf("Voitures disponibles :\n");
                for (int i = 0; i < nombreVoitures; i++) {
                    if (strcmp(voitures[i].etat, "Disponible") == 0) {
                        printf("%d. %s %s\n", i + 1, voitures[i].marque, voitures[i].modele);
                    }
                }
                int choixVoiture;
                do {
                    printf("Choisissez une voiture : ");
                    scanf("%d", &choixVoiture);
                } while (choixVoiture < 1 || choixVoiture > nombreVoitures);

                louerVoiture(&voitures[choixVoiture - 1], &historique, client, debut, fin);
                break;

            case 2:
                printf("Entrez le numero de la voiture a retourner : ");
                scanf("%d", &choixVoiture);
                printf("Entrez la date de retour (jj mm aa) : ");
                scanf("%d %d %d", &dateRetour.jj, &dateRetour.mm, &dateRetour.aa);

                retournerVoiture(&voitures[choixVoiture - 1], &historique, dateRetour);
                break;

            case 3:
                afficherHistorique(&historique);
                break;
            case 4:
                printf("Entrez le mois pour afficher l'historique (1-12) : ");
                scanf("%d", &mois);
                printf("Entrez l'année : ");
                scanf("%d", &annee);
                afficherHistoriqueParMois(&historique, mois, annee);
                break;
            case 5:

                printf("Entrez la date pour afficher l'historique (jj mm aa) : ");
                scanf("%d%d%d",&jour, &mois,&annee);
                afficherHistoriqueParJour(&historique,jour,mois,annee);
                break;
            case 6:
                printf("Voitures disponibles :\n");
                for (int i = 0; i < nombreVoitures; i++) {
                    if (strcmp(voitures[i].etat, "Disponible") == 0) {
                        printf("%d. %s %s\n", i + 1, voitures[i].marque, voitures[i].modele);
                    }
                }
                printf("Entrez le numero de la voiture pour afficher sa description : ");
                scanf("%d", &choixVoiture);

                if (choixVoiture >= 1 && choixVoiture <= nombreVoitures) {
                    afficherDescription(&voitures[choixVoiture - 1]);
                } else {
                    printf("Numero de voiture invalide.\n");
                }
                break;


            case 7:
                supprimerVoitureEnPanne(voitures,&nombreVoitures);
                break;

            case 8:
                    if (nombreVoitures > 0) {
                    afficherVoituresDisponibles(voitures,nombreVoitures);
                    printf("Entrez le numero de la voiture a modifier : ");
                    scanf("%d", &choixVoiture);

                    if (choixVoiture >= 1 && choixVoiture <= nombreVoitures) {
                        modifierVoiture(&voitures[choixVoiture-1]);
                    } else {
                        printf("Numero de voiture invalide.\n");
                    }
                } else {
                    printf("Aucune voiture disponible a modifier.\n");
                }
                break;

            case 9:
                printf("Entrez le nom du client : ");
                scanf("%s", client);

                do {
                    printf("Entrez la date de debut de la reservation (jj mm aa) : ");
                    if (scanf("%d %d %d", &debut.jj, &debut.mm, &debut.aa) != 3 || !estDateValide(&debut)) {
                        printf("Format de date invalide. Veuillez entrer une date valide sous la forme (jj mm aa).\n");
                        while (getchar() != '\n');
                    }
                } while (!estDateValide(&debut));

                do {
                    printf("Entrez la date de fin de la reservation (jj mm aa) : ");
                    if (scanf("%d %d %d", &fin.jj, &fin.mm, &fin.aa) != 3 || !estDateValide(&fin)) {
                        printf("Format de date invalide. Veuillez entrer une date valide sous la forme (jj mm aa).\n");
                        while (getchar() != '\n');
                    }
                } while (!estDateValide(&fin));

                printf("Voitures disponibles :\n");
                for (int i = 0; i < nombreVoitures; i++) {
                    if (strcmp(voitures[i].etat, "Disponible") == 0) {
                        printf("%d. %s %s\n", i + 1, voitures[i].marque, voitures[i].modele);
                    }
                }

                int choixVoitureRes;
                do {
                    printf("Choisissez une voiture : ");
                    scanf("%d", &choixVoitureRes);

                } while (choixVoitureRes < 1 || choixVoitureRes > nombreVoitures);

                reserverVoiture(&voitures[choixVoitureRes - 1], &historique, client, debut, fin);
                break;
            case 10:
                if (historique.nombreReservations > 0) {
                    afficherReservations(&historique);
                    int choixConfirmation;
                    printf("Entrez le numero de reservation a confirmer : ");
                    scanf("%d", &choixConfirmation);
                    confirmerReservation(&historique, choixConfirmation);
                } else {
                    printf("Aucune reservation dans l'historique.\n");
                }
                break;

           case 11:
                if (historique.nombreReservations > 0) {
                    afficherReservations(&historique);
                    int choixAnnulation;
                    printf("Entrez le numero de reservation a annuler : ");
                    scanf("%d", &choixAnnulation);
                    annulerReservation(&historique, choixAnnulation);
                } else {
                    printf("Aucune reservation dans l'historique.\n");
                }
                break;


            case 12:
                afficherReservations(&historique);
                break;

            case 13:
                break;
            default:
                printf("choix invalide veillez choisir un entier entre 1 et 13");
}} while (choix!=13);

    free(historique.locations);
    free(historique.reservations);

    return 0;
}

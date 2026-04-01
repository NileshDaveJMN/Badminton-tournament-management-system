# Badminton-tournament-management-system

import math

# --- STEP 1: Player Blueprint ---
class Player:
    def __init__(self, name, city, rank, gender):
        self.name = name
        self.city = city
        self.rank = int(rank)
        self.gender = gender.upper()
        self.seed = None

# --- STEP 1 & 2: Entries & Seeding ---
def get_all_entries():
    male_list = []
    female_list = []
    print("=== Badminton Registration (Type 'DONE' in name to stop) ===")
    
    while True:
        name = input("\nPlayer Name: ")
        if name.upper() == "DONE": break
        
        gender = input("Gender (M/F): ").upper()
        if gender not in ['M', 'F']:
            print("⚠️ Sirf M ya F likhen.")
            continue
            
        city = input("City: ")
        rank = input("Current Rank: ")
        p = Player(name, city, rank, gender)
        
        if gender == 'M': male_list.append(p)
        else: female_list.append(p)
        print(f"✅ {name} add ho gaya.")

    # Seeding (Sorting by Rank)
    male_list.sort(key=lambda x: x.rank)
    female_list.sort(key=lambda x: x.rank)
    
    for i, p in enumerate(male_list): p.seed = i + 1
    for i, p in enumerate(female_list): p.seed = i + 1
    
    return male_list, female_list

# --- STEP 3 & 4: Draw Size & Bracket ---
def prepare_bracket(player_list):
    count = len(player_list)
    if count < 2: return None, 0
    
    # Draw Size Logic
    for size in [8, 16, 32, 64, 128]:
        if count <= size:
            draw_size = size
            break
            
    # Professional Bracket (Seed 1 Top, Seed 2 Bottom)
    bracket = [None] * draw_size
    bracket[0] = player_list[0] # Seed 1
    if count > 1:
        bracket[draw_size - 1] = player_list[1] # Seed 2
    
    # Baaki players ko bharna
    idx = 2
    for i in range(1, draw_size - 1):
        if idx < count:
            bracket[i] = player_list[idx]
            idx += 1
    return bracket, draw_size

# --- STEP 5: Match & Score Execution ---
def play_tournament(bracket, category):
    print(f"\n{'='*10} {category.upper()} TOURNAMENT START {'='*10}")
    current_round = bracket
    round_no = 1
    
    while len(current_round) > 1:
        print(f"\n--- ROUND {round_no} ---")
        next_round = []
        
        for i in range(0, len(current_round), 2):
            p1, p2 = current_round[i], current_round[i+1]
            
            # Match Logic
            if p1 is None and p2 is None:
                next_round.append(None)
            elif p1 is None or p2 is None:
                winner = p1 if p1 else p2
                print(f"Match: {winner.name} gets a BYE.")
                next_round.append(winner)
            else:
                print(f"Match: {p1.name} (S{p1.seed}) vs {p2.name} (S{p2.seed})")
                p1_sets, p2_sets = 0, 0
                for s in range(1, 4):
                    s1 = int(input(f"  {p1.name} Set {s} points: "))
                    s2 = int(input(f"  {p2.name} Set {s} points: "))
                    if s1 > s2: p1_sets += 1
                    else: p2_sets += 1
                    if p1_sets == 2 or p2_sets == 2: break
                
                winner = p1 if p1_sets == 2 else p2
                print(f"🏆 Winner: {winner.name}")
                next_round.append(winner)
        
        current_round = next_round
        round_no += 1
    
    print(f"\n🎉 CHAMPION: {current_round[0].name} 🎉")

# --- MAIN EXECUTION ---
if __name__ == "__main__":
    m_list, f_list = get_all_entries()
    
    if m_list:
        m_bracket, m_size = prepare_bracket(m_list)
        play_tournament(m_bracket, "Male")
    
    if f_list:
        f_bracket, f_size = prepare_bracket(f_list)
        play_tournament(f_bracket, "Female")
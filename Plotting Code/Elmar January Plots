import numpy as np
import matplotlib.pyplot as plt
from scipy.signal import find_peaks



def voltage_to_displacement(voltage,direction):
    piezo_data = np.genfromtxt("Piezo_manufacturer_data.csv",
                               delimiter=",",
                                skip_header=2)
    voltage_index = piezo_data[:,0]
    if direction=="up": piezo_displacement = piezo_data[:,1]
    elif direction=="down": piezo_displacement = piezo_data[:,2]
    else: 
        print("Direction set to up automatically")

    for i in voltage_index:
        if (voltage < i):
            index = np.where(voltage_index == i)[0]
            rest = voltage - voltage_index[index-1]
            break
    
    displacement = piezo_displacement[index-1] + rest/10 * (piezo_displacement[index]-piezo_displacement[index-1])
    return displacement # in mu

def displacement_to_voltage(displacement,direction):
    piezo_data = np.genfromtxt("Piezo_manufacturer_data.csv",
                               delimiter=",",
                                skip_header=2)
    voltage_index = piezo_data[:,0]
    if direction=="up": 
        piezo_displacement = piezo_data[:,1]
    elif direction=="down":
        piezo_displacement = piezo_data[:,2]
    else:
        piezo_displacement = piezo_data[:,1]
        print("Direction set to up automatically")
    

    for i in piezo_displacement:
        if (displacement < i):
            index = np.where(piezo_displacement == i)[0]
            rest = displacement - piezo_displacement[index-1]
            break
    
    voltage = voltage_index[index-1] + rest*10 /(piezo_displacement[index]-piezo_displacement[index-1]) 
    return voltage


def plotter(input):
    # Import der Messdaten
    CH_1,CH_2,outfile,title = input
    CH_1_data = np.genfromtxt(CH_1,
                        delimiter=',',
                        skip_header=0)
    CH_2_data = np.genfromtxt(CH_2,
                        delimiter=',',
                        skip_header=0)
    
    plt.close()
    volt_per_second = 3193 #V per s
    wavelength = 0.980 # mu
    voltage_amplification = 15 

    def triangular(x, a, b, d):
        return d + a*abs(x-b)
    
    popt = [-3.19390532e+03, 1.27239090e-03, 1.00287261e+01]


    voltage_funkgenerator = CH_2_data[:,4]
    time = CH_1_data[:,3]
    voltage_PD = CH_1_data[:,4]

    peak_index,prop = find_peaks(voltage_PD,height=1.3,distance=70,width=50)

    f = plt.figure(figsize=(10, 4))
    ax = f.subplots(1, 1)
    ax.set_title(title)
    ax.plot(time,voltage_funkgenerator,label = "Voltage Funktiongenerator")
    ax.plot(time,voltage_PD, label = "Voltage PD")
    # ax.scatter(time[peak_index],voltage_PD[peak_index], label = "Peacks",color= "red",marker="o")
    # ax.vlines(time[peak_index],0,10,colors="black")
    starting_peaks = [1,4]
    for i in starting_peaks:
        if i%2==0: 
            direction = "down"
            wavelength_x = -wavelength
        else: 
            direction = "up"
            wavelength_x = wavelength

        start_voltage = triangular(time[peak_index[i]],*popt)*voltage_amplification
        print ("i:" + str(i))
        print("start_voltage:" + str(start_voltage))
        start_displacement = voltage_to_displacement(start_voltage,direction)
        print("start displacement" + str(start_displacement))
        end_displacement_1 = start_displacement + wavelength_x/2
        print("end displacement" + str(end_displacement_1))
        end_voltage_1 = displacement_to_voltage(end_displacement_1,direction)
        print("end voltage" + str(end_voltage_1))
        peak_interval_1  =  abs(end_voltage_1-start_voltage)/voltage_amplification /volt_per_second 
        
        ax.vlines(time[peak_index[i]],0,10,colors="green")
        ax.vlines(time[peak_index[i]]+peak_interval_1,0,10,colors="red")
        end_displacement_2 = start_displacement + wavelength_x
        print("end_displacement 2:" + str(end_displacement_2))
        end_voltage_2 = displacement_to_voltage(end_displacement_2,direction)
        print("end voltage 2:" + str(end_voltage_2))
        peak_interval_2  =  abs(end_voltage_2-start_voltage)/voltage_amplification /volt_per_second 
        ax.vlines(time[peak_index[i]]+peak_interval_2,0,10,colors="red")
        ax.vlines(time[peak_index[i+1]],0,10,colors="black")
        try:
            ax.vlines(time[peak_index[i+2]],0,10,colors="black")
        except: print("out of bound")
    ax.set_xlabel('Time [s]') 
    ax.set_ylabel('Voltage [V]', color = 'black')
    f.savefig(outfile)

if __name__ == "__main__":
    l = [
        ["F0010CH1.CSV","F0010CH2.CSV", "all_data_expected_2.png","measurement data"],
    ]
    plotter(l[0])

    print(voltage_to_displacement(45,"up"))
    print(displacement_to_voltage(voltage_to_displacement(45,"up"),"up"))
    print(voltage_to_displacement(45,"down"))
    print(displacement_to_voltage(voltage_to_displacement(45,"down"),"down"))

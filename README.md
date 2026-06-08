from ctypes import *
import random
import os
import cv2
import time
import darknet
import argparse
from threading import Thread, enumerate
from queue import Queue
from djitellopy import Tello
import cv2, math, time




def parser():
    parser = argparse.ArgumentParser(description="YOLO Object Detection")
    parser.add_argument("--input", type=str, default=0,
                        help="video source. If empty, uses webcam 0 stream")
    parser.add_argument("--out_filename", type=str, default="",
                        help="inference video name. Not saved if empty")
    parser.add_argument("--weights", default="yolov4-custom_testtt2.weights",
                        help="yolo weights path")
    parser.add_argument("--dont_show", action='store_true',
                        help="windown inference display. For headless systems")
    parser.add_argument("--ext_output", action='store_true',
                        help="display bbox coordinates of detected objects")
    parser.add_argument("--config_file", default="./cfg/yolov4-custom_drone2.cfg",
                        help="path to config file")
    parser.add_argument("--data_file", default="./cfg/obj_drone2.data",
                        help="path to data file")
    parser.add_argument("--thresh", type=float, default=.25,
                        help="remove detections with confidence below this value")
    return parser.parse_args()


def str2int(video_path):
    """
    argparse returns and string althout webcam uses int (0, 1 ...)
    Cast to int if needed
    """
    try:
        return int(video_path)
    except ValueError:
        return video_path


def check_arguments_errors(args):
    assert 0 < args.thresh < 1, "Threshold should be a float between zero and one (non-inclusive)"
    if not os.path.exists(args.config_file):
        raise(ValueError("Invalid config path {}".format(os.path.abspath(args.config_file))))
    if not os.path.exists(args.weights):
        raise(ValueError("Invalid weight path {}".format(os.path.abspath(args.weights))))
    if not os.path.exists(args.data_file):
        raise(ValueError("Invalid data file path {}".format(os.path.abspath(args.data_file))))
    if str2int(args.input) == str and not os.path.exists(args.input):
        raise(ValueError("Invalid video path {}".format(os.path.abspath(args.input))))


def set_saved_video(input_video, output_video, size):
    fourcc = cv2.VideoWriter_fourcc(*"MJPG")
    fps = int(input_video.get(cv2.CAP_PROP_FPS))
    video = cv2.VideoWriter(output_video, fourcc, fps, size)
    return video


def video_capture(frame_queue, darknet_image_queue):
    


    while cap.isOpened():     
        #ret, frame = cap.read()
        frame = frame_read.frame
        #if not ret:
            #break
        frame_rgb = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)
        frame_resized = cv2.resize(frame_rgb, (960, 720),
                                   interpolation=cv2.INTER_LINEAR)
        frame_queue.put(frame_resized)
        darknet.copy_image_from_bytes(darknet_image, frame_resized.tobytes())
        darknet_image_queue.put(darknet_image)
    cap.release()


  
def inference(darknet_image_queue, detections_queue, fps_queue):
    while cap.isOpened():
        darknet_image = darknet_image_queue.get()
        prev_time = time.time()
        detections = darknet.detect_image(network, class_names, darknet_image, thresh=args.thresh)
        detections_queue.put(detections)
        fps = int(1/(time.time() - prev_time))
        fps_queue.put(fps)
        print("FPS: {}".format(fps))
        darknet.print_detections(detections, args.ext_output)
    cap.release()


def forward():
    tello.move_forward(30)
    #notrun = True
def back():
    tello.move_back(30)
    #notrun = True
def left():
    tello.move_left(20)
    #notrun = True
def right():
    tello.move_right(20)
    #notrun = True
def clockleft():
    tello.rotate_counter_clockwise(30)
    #notrun = True
def clockright():
    tello.rotate_clockwise(30)
def up():
    tello.move_up(20)
    #notrun = True
def down():
    tello.move_down(20)
    #notrun = True
def takeoff():
    tello.takeoff()
    tello.move_up(20)
def land():
    tello.land() 
def move():
    tello.move_forward(180)  #20-500
    #notrun = True
def clock():
    tello.rotate_clockwise(360)

#notrun = True

def drawing(frame_queue, detections_queue, fps_queue):
    random.seed(3)  # deterministic bbox colors 隨機顏色
    video = set_saved_video(cap, args.out_filename, (960, 720))
    while cap.isOpened():
        frame_resized = frame_queue.get()
        detections = detections_queue.get()
        fps = fps_queue.get()
        darknet.print_detections(detections, True)
        if frame_resized is not None:
            image = darknet.draw_boxes(detections, frame_resized, class_colors)
            image = cv2.rectangle(image, (420, 175), (540, 295), (0, 255, 0), 1)     #中央框框
            image = cv2.cvtColor(image, cv2.COLOR_BGR2RGB)
            for label, confidence, bbox in detections:
                x, y, w, h = bbox
                box_x = int(x)
                box_y = int(y)
                boximage = cv2.circle(image, (box_x, box_y),5, (255, 0, 0), -1)      #box中央點
                if 420 <= box_x <= 540 and 175 <= box_y <= 295:
                    if (time.time()-t0)%3>2.95:
                        Thread(target=move, args=()).start()
                        print("gogogogogogogogogogogogogogogogogogogogogogogogogogo")
                elif box_y <=175:
                    #if notrun:
                        #notrun = False
                     if (time.time()-t0)%3>2.95:
                        Thread(target=up, args=()).start()
                        print("往上往上往上往上往上往上往上往上往上往上往上往上往上")
                        print(time.time(),t0,"---------",time.time()-t0)
                elif box_y >=295:
                    #if notrun:
                        #notrun = False
                     if (time.time()-t0)%3>2.95:
                        Thread(target=down, args=()).start()
                        print("往下往下往下往下往下往下往下往下往下往下往下往下往下")
                        print(time.time(),t0,"---------",time.time()-t0)
                elif box_x <=420 and 175 <= box_y <= 295:
                    #if notrun:
                        #notrun = False
                     if (time.time()-t0)%3>2.95:
                        Thread(target=left, args=()).start()
                        print("往左往左往左往左往左往左往左往左往左往左往左往左往左")
                        print(time.time(),t0,"---------",time.time()-t0)
                elif box_x >=540 and 175 <= box_y <= 295:
                    #if notrun:
                        #notrun = False
                     if (time.time()-t0)%3>2.95:
                        Thread(target=right, args=()).start()
                        print("往右往右往右往右往右往右往右往右往右往右往右往右往右")
                        print(time.time(),t0,"---------",time.time()-t0)

            if args.out_filename is not None:
                video.write(image)
            if not args.dont_show:
                cv2.imshow('Inference', image)
            key = cv2.waitKey(1) & 0xff
            if key == 27: # ESC
                Thread(target=land, args=()).start()
                break
            elif key == ord('w'):
                Thread(target=forward, args=()).start()
            elif key == ord('s'):
                Thread(target=back, args=()).start()
            elif key == ord('a'):
                Thread(target=left, args=()).start()
            elif key == ord('d'):
                Thread(target=right, args=()).start()
            elif key == ord('e'):
                Thread(target=clockright, args=()).start()
            elif key == ord('q'):
                Thread(target=clockleft, args=()).start()
            elif key == ord('r'):
                Thread(target=up, args=()).start()
            elif key == ord('f'):
                Thread(target=down, args=()).start()
            elif key == ord('t'):
                Thread(target=takeoff, args=()).start()
            elif key == ord('l'):
                Thread(target=land, args=()).start()

    cap.release()
    video.release()
    cv2.destroyAllWindows()


def print_detections(detections, coordinates=True):
    print("\nObjects:")
    for label, confidence, bbox in detections:
        x, y, w, h = bbox
        if coordinates:
            print("{}: {}%    (left_x: {:.0f}   top_y:  {:.0f}   width:   {:.0f}   height:  {:.0f})".format(label, confidence, x, y, w, h))
        else:
            print("{}: {}%".format(label, confidence))




if __name__ == '__main__':

    tello = Tello()
    tello.connect()
    tello.streamon()
    frame_read = tello.get_frame_read()
    t0=time.time()

    frame_queue = Queue()
    darknet_image_queue = Queue(maxsize=1)
    detections_queue = Queue(maxsize=1)
    fps_queue = Queue(maxsize=1)

    args = parser()
    check_arguments_errors(args)
    network, class_names, class_colors = darknet.load_network(
            args.config_file,
            args.data_file,
            args.weights,
            batch_size=1
        )
    # Darknet doesn't accept numpy images.
    # Create one with image we reuse for each detect
    width = darknet.network_width(network)
    height = darknet.network_height(network)
    darknet_image = darknet.make_image(960, 720, 3)
    input_path = str2int(args.input)
    cap = frame_read.cap
    Thread(target=video_capture, args=(frame_queue, darknet_image_queue)).start()
    Thread(target=inference, args=(darknet_image_queue, detections_queue, fps_queue)).start()
    Thread(target=drawing, args=(frame_queue, detections_queue, fps_queue)).start()
